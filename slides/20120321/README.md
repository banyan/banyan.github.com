RSpec::Expectations
=============
<br />
<address>Kohei Hasegawa@banyan<br />paperboy&co.<br />20120321</address>
<!-- data-x="-18000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

RSpec 四天王
----------
<!-- data-x="-9000" -->
<!-- data-y="-1500" -->
<!-- data-rotate-y="90" -->

Rspec が構成される module (rspec-rails は rails に関するライブラリ）

* rspec-core
* rspec-expectations
* rspec-mocks
* rspec-rails

ドキュメント
----------
[https://www.relishapp.com/rspec](https://www.relishapp.com/rspec) に詳細があります。

今日は rspec-expectations について調べてみました
----------
<br />
「ククク... 奴は四天王の中でも最弱...」

動機
----------
* rspec-expectations は使い方がすぐ出てくるけど、obj.should be_nil とか、obj.should == 1 とか色々ありすぎて覚えきれない
* rspec-expectations は分かった気になるけど、ちゃんと理解してない感が強かったので発表して覚えるメソッド

まずエクスペクテーションとは?
----------

サブジェクトコードに期待される振る舞いを指定するコード

こんな感じ
----------

    result.should == 37
    false.should_not be_nil

当然のように should, should_not が出てきてますがこれはどこで定義しているのでしょうか?
----------

まずディレクトリ構成を見てみます
----------

    rspec-expectations/lib
    └── rspec
        ├── expectations
        │   └── extensions
        └── matchers
            ├── built_in
            └── extensions

rspec-expectations は呼び出されると
----------
rspec-expectations/lib/rspec/expectations.rb

      1 require 'rspec/expectations/extensions'

一番最初に extensions 以下のファイルを require して、
Ruby の動的な特徴であるオープンクラス(モンキーパッチ）を利用してオブジェクトを拡張します

extensions.rb では kernel, array, object の拡張をしますが、
----------

rspec-expectations/lib/rspec/expectations/extensions.rb

      1 require 'rspec/expectations/extensions/kernel'
      2 require 'rspec/expectations/extensions/array'
      3 require 'rspec/expectations/extensions/object'

should の定義は kernel で定義されている
----------

    module Kernel は全てのクラスから参照できるメソッドを定義しているモジュール。 Object クラスはこのモジュールをインクルードしています。
    Object クラスのメソッドは実際にはこのモジュールで定義されています。これはトップレベルでのメソッドの再定義に対応するためです。

should/should_not は、Object に対する拡張なので、Class を含めたあらゆるオブジェクトで利用できます。

should の定義は kernel で定義されている2
----------

    module Kernel
      def foo
        1
      end
    end

    class Bar
      def hoge
        3
      end
    end

    bar = Bar.new
    p bar.hoge # => 3
    p bar.hoge.foo # => 1
    p bar.foo # => 1

should の定義は kernel で定義されている3
----------
もしテストをするクラスに should というメソッドが定義されている場合、
obj.should == 3 などは動かない。

    # def should が obj に定義されてない
    p obj.should # => <RSpec::Matchers::BuiltIn::PositiveOperatorMatcher:0x007f87f136c5b0 @actual=#<Bowling:0x007f87f136c5d8>

    def should # が定義されていると、
      3
    end

    p obj.should # 単純に3が実行されるだけ

should の実体
----------
lib/rspec/expectations/extensions/kernel.rb

    def should(matcher=nil, message=nil, &block)
      RSpec::Expectations::PositiveExpectationHandler.handle_matcher(self, matcher, message, &block)
    end

should の実体2
----------
should は RSpec::Expectations::PositiveExpectationHandler、
should_not は RSpec::Expectations::NegativeExpectationHandler に渡しているだけ

should の実体3
----------
lib/rspec/expectations/handler.rb

      1 module RSpec
      2   module Expectations
      3     class PositiveExpectationHandler
      4       def self.handle_matcher(actual, matcher, message=nil, &block)
      5         ::RSpec::Matchers.last_should = :should
      6         ::RSpec::Matchers.last_matcher = matcher
      7         return ::RSpec::Matchers::BuiltIn::PositiveOperatorMatcher.new(actual) if matcher.nil?
      8
      9         match = matcher.matches?(actual, &block)
     10         return match if match

should の実体4
----------
もし matcher がなければ ::RSpec::Matchers::BuiltIn::PositiveOperatorMatcher.new(actual)
を実行して、matcher が存在すれば、matcher の持つ、matches? メソッドを呼び出すというところまで分かりました


matcher とは何か
----------
A Matcher is any object that responds to the following methods:

        matches?(actual)
        failure_message_for_should

この2個のメソッドに応答する object であればそれが matcher

built_in matcher
----------
    lib/rspec/matchers/
    ├── built_in
    │   ├── base_matcher.rb
    │   ├── be.rb
    │   ├── be_instance_of.rb
    │   ├── be_kind_of.rb
    │   ├── be_within.rb
    │   ├── change.rb
    │   ├── cover.rb
    │   ├── eq.rb
    │   ├── eql.rb
    │   ├── equal.rb
    │   ├── exist.rb
    │   ├── has.rb
    │   ├── have.rb
    │   ├── include.rb
    │   ├── match.rb
    │   ├── match_array.rb
    │   ├── raise_error.rb
    │   ├── respond_to.rb
    │   ├── satisfy.rb
    │   └── throw_symbol.rb

should の流れ
----------

    result.should equal(5)

この場合、equal が matcher に当たる。Ruby インタープリタは equal(5) を先に評価して(マッチャオブジェクト）を返し、result.should の引数となります

(equal の定義は rspec-expectations/lib/rspec/matcher.rb で Factory Method で返す)

    390     def equal(expected)
    391       BuiltIn::Equal.new(expected)
    392     end

マッチャオブジェクトが matches? メソッドを actual (self) を引数にして実行する!!

    match = matcher.matches?(actual, &block)

したがって基本的なエクスペクテーションの記述は以下のようになる
----------

    actual.should matcher(expected)
    actual.should_not matcher(expected)

※例外として expected に加えて補助的な引数を渡すものや、メソッドチェインするものが存在するのでこれが全てではないがこの形が基本

matchers について細かくみていきます
----------

演算子マッチャ1
----------
RSpec では次の演算子をマッチャとして利用できます。

* <
* <=
* ==
* ===
* =~
* >
* >=

演算子マッチャ2
----------
こういう風に使う

    obj.should > 5
    obj.should == "hoge"

演算子マッチャ3
----------
否定演算子 (!=, !~) がサポートされてないのは、

    obj.should == 5
    obj.should.==(5) # == がメソッドで実際はこう解釈されてるが
    obj.should != 5
    !(obj.should.==(5)) # こう解釈されてしまうから使えなくなるとのこと

    obj.should_not == 5 # 否定をしたい場合は should_not を使う

演算子マッチャ4
----------
演算子マッチャは "be" matcher をつけても動く

    37.should be < 100
    37.should be <= 38
    37.should be >= 2
    37.should be > 7

述語マッチャ1
----------
配列が空であることをテストする

Ruby に直接組み込まれているメソッドは array.empty?<br />

    [].empty?.should == true

分かりにくい

述語マッチャ2
----------
こう書ける

    [].should be_empty

* array に empty? メソッドが存在する場合に、成功する

述語マッチャ3
----------
* でもどこで定義されてるの？
* method_missing をオーバーライドして、マッチャが be_ からはじまっていたら BePredicate に渡し、"be_" を取り除き、"?" を付け足し、その結果のメッセージを指定されたオブジェクトに渡す

述語マッチャ4
----------
lib/rspec/matchers/built_in/be.rb


    122       class BePredicate < Be
    123         def initialize(*args, &block)
    124           @expected = parse_expected(args.shift)
    ...
    166         def parse_expected(expected)
    167           @prefix, expected = prefix_and_expected(expected)
    168           expected
    169         end
    170
    171         def prefix_and_expected(symbol)
    172           symbol.to_s =~ /^(be_(an?_)?)(.*)/
    173           return $1, $3
    174         end

述語マッチャ5
----------
    user.should be_in_role("admin")

このサンプルは、user.in_role?("admin") が true を返す限り成功する

述語マッチャ6
----------

    be_a_kind_of(*args)
    be_an_instance_of(*args)

    "foo".should be_an_instance_of(String)     # Exact class
    "foo".should_not be_an_instance_of(Object) #

    "foo".should be_a_kind_of(Object)          # Match subclasses
    "foo".should be_an(Object)                 #

値が変化したことをテストしたい - change
----------

    expect{ array << 42 }.to change{ array.size }.from(0).to(1)
    expect{ array << 42 }.to change{ array.size }.by(1)

例外が発生することを確認したい - raise_error
----------

    expect{ subject.bad_method }.to raise_error(NoMethodError)

ついでにシンボルが throw されるかも - throw_symbol
----------

    expect{ subject.bad_method }.to throw_symbol

いくつ存在してるかを確かめたい - have
----------
    [1,2,3].should have(3).items
    [1,2,3].should have_exactly(3).items # have_exactly は have の alias
    [1,2,3].should have_at_least(2).items # 少なくとも n 個は持ってるはず
    [1,2,3].should have_at_most(4).items # 多くとも n 個だろう

まだありますが今日はこのへんで...
----------
* matcher 自体は16個が relish では載っている。全部覚える必要はないが、とりあえず目を通すとよさげ

まとめ
----------
* RSpec は 全てのオブジェクトに should と should_not を足す
  * rspec-expectations の根幹
* actual.should mathers(expected) という基本形を覚えておけば、matchers さえ覚えていけば対応できる
* mathcer はカスタムマッチャが簡単に作れるようになってるので、自分でも作ることが可能

ご清聴ありがとうございました
----------
<!-- data-x="0" -->
<!-- data-y="-12000" -->
<!-- data-rotate-y="90" -->

参考
----------

* The RSpec Book
* http://www.slideshare.net/Sixeight/introduce-rspecs-matchers
* http://kerryb.github.com/iprug-rspec-presentation
