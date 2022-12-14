# オブジェクト指向プログラミングの基本「カプセル化」「ポリモーフィズム」
# ①カプセル化
## 1.関数型言語の登場  

## 1-1.特定の処理を関数として抽象化することで、再利用が可能になった

名前を見れば何をしているかがわかる。

例  
```
$aria = $bottom * $height / 2;
```
↓
```
public function calculate_aria_of_triangle($bottom, $height) {
    $aria = $bottom * $height/2;
    eho $area;
}
```

## 1-2.関数型言語でもまだ足りない？
では以下のじゃんけんゲームの場合はどうか。
```
pub func judge($自分の手,$相手の手)
{
    じゃんけんのルールに従い、勝敗(2,1,0)を返す
}

pub func play($自分の手,$相手の手)
{
    result=judge($自分の手,$相手の手);
    show_result($result);
}
```

ここで改修が発生！
```
勝敗が数値じゃ分かりづらいからwin,draw,loseに変更しよう。  
```
じゃあ、、、
judge関数を以下のように修正！
```
pub func judge(自分の手,相手の手)
{
    じゃんけんのルールに従い、勝敗(win,draw,lose)を返す
}
```

変更箇所はこれだけで本当に大丈夫か？

ちなみに
```
pub func play(自分の手,相手の手)
{
    result=judge(自分の手,相手の手);


    //この関数は期待通りに動くのか？
    show_result(result);
}
```
こっちも変更が必要だった。

もしもっとプログラムが大規模になり、何千、何万行分も関数を定義したりしていたら、、、
このように修正漏れの可能性が出てくる。


## 関数型言語の解決すべき問題
### __関数と関数の依存関係を整理できない(整合性が保たれるべき範囲を示せない)__ ため、修正に手間がかかり、修正漏れが起きる可能性がある

## カプセル化で解決
### __クラスによって整合性を保つ範囲を示す__

- __「クラスの中では整合性を保つ」__ = __「クラス内の要素を変更する際は、クラス内でほかに変更箇所がないか探す」__
- クラスを定義する際は関数の依存関係を意識する。

上記のじゃんけんゲームでは __「playメソッドはjudgeメソッドに依存する」__ = __「両者の間で整合性が保たれていなければならない」__

- play=クライアント（依存する側）
- judge＝プロバイダ（処理を提供している側）

このように依存関係のある関数の集まり(業務システムでは業務ごとのビジネスロジック等が代表的)を1つのクラスとして閉じ込め、整合性を保つ範囲を示す。
```
class jankenGame
{
    public function play

    // 修正する際はクラス内で他に修正箇所が無いかを必ず確認する
    public function judge

    etc...
}
```

- 関数型言語

〇〇の関数の処理を変更したい→その関数の依存関係を全体から探し、修正しなければならない

- クラス

〇〇のメソッドの処理を変更したい→そのクラスの中に依存関係のあるメソッドが集合しているので、そのクラス内で他の変更箇所を探すだけで良い


## クラスの使い方
クラスのメソッド、プロパティの値を使いたいのであれば、実体化(インスタンス化=メモリを確保＋初期化)する必要がある。
飛行機の設計図だけではなく、それを実体化させて初めて空を飛べることに似ているかも。


### インスタンス化
```
$game=new jankenGame();
//インスタンス化すると、メソッド等が使える
$game->play(1,2)
```

インスタンス化でやっていること「メモリ確保」とは？

実は普段からみんなやってる
```
$message = 'hello';
// イメージで書き換えると。。。
$message = new String([h,e,l,l,o]);
```
変数を定義する際にメモリを割り当てるのと基本的には同じ。

実際に
$message = 'hello';のように定義したり、既存の関数でstring型を返すことではじめて
$message->str_〇〇()
のようにstring型用の関数が使えるようになったり、そもそもstring型に対して使える関数は決められているのも、クラス内にメソッドを閉じ込めることに似ているかも。


### $thisとは
クラスのメソッドからクラスのメソッドを呼び出す時、クラスの中(正確にはそのクラスのインスタンス自身)であることを示す。(phpは関数単体でも定義できてしまうので、同一クラス内のメソッドを呼び出す際はこのような記述が必要)

### クラスを使って書き換える
クラスを使用すると、プロバイダはそのクラスから作られたインスタンスをクライアントに提供する。

処理を提供する側（プロバイダ）はクラスのインスタンス自身となり、クライアントから見てクラス内の情報を隠すことができる。
```
// プロバイダ
class jankenGame
{
    public function judge($自分の手,$相手の手)
    {
    じゃんけんのルールに従い、勝敗(2,1,0)を出力
    }

    public function play($自分の手,$相手の手)
    {
        $result=judge(自分の手,相手の手);
        show_result($result);
    }
}
```


```
// クライアントでの処理
$game = new jankenGame()
$game->play($自分の手,$相手の手);
```
こうなることで何が良いのか。

先ほどと同じ改修「勝敗が数値じゃ分かりづらいからwin,draw,loseに変更しよう」を行う場合。

↓

クライアント側の処理の変更は必要か？

↓

必要ない。なぜならクラス内部で整合性が保たれているため、依存関係のある処理の変更すらもクラスの内部に閉じ込めることができるから。

## カプセル化まとめ
- クラスの中では整合性を保つ
- クラス単位で提供・再利用する
- クライアントはメソッドを利用する
   
→これにより変更、修正が容易になる



# ②ポリモーフィズム多態性

### ある1つのメソッドの呼び出しに対して、クラスごとに異なる処理を行うこと
  
(=異なる処理を同じメソッドで行うこと)


例えばじゃんけんゲーム
```
show_result($result)
{
    $result = 1なら勝利と出力
    $result = 0なら引き分けと出力
    それ以外なら敗北と出力
}
```
これ自体はok
```
改修
「英語対応してほしい」
```
```
show_result($result, $lang)
{
if ($lang === 'ja') {

    $result = 1なら勝利と出力
    $result = 0なら引き分けと出力
    それ以外なら敗北と出力

} else {

    $result = 1ならwinと出力
    $result = 0ならdrawと出力
    それ以外ならloseと出力

}
}
```
```
さらに改修
「中国語、フランス語、ドイツ語も対応して！」
```

このまま☝️の条件分岐増やし続けるの？複雑さは条件分岐の多さに宿る。単純な処理でも条件分岐が多いと、読み手に抵抗を感じさせる。
条件分岐を減らさなければならない。

そこで言語ごとに下記のようにクラスを作る
```
class japaneseDisplay
{
    public function show($result)
    {
        $result = 1なら勝利と出力
        $result = 0なら引き分けと出力
        それ以外なら敗北と出力
    }
}


class englishDisplay
{
    public function show($result)
    {
        $result = 1ならwinと出力
        $result = 0ならdrawと出力
        それ以外ならloseと出力
    }
}

class chineseDisplay
{
同様
}

class franchDisplay
{
同様
}
```
そしてプロバイダであるじゃんけんゲームクラスを下記に変更。
```
class jankenGame
{
    public function __construct($display) {
        $this->display=$display;
    }

    public function play($自分の手、$相手の手)
    {
        $result=judge($自分の手、$相手の手);
        $this->display->show($result);
    }
}
```

クライアント側
```
// 日本語
$display = new japaneseDisplay();
$game = new jankenGame($display);
$game->play($自分の手、$相手の手);

// 英語
$display = new englishDisplay();
$game = new jankenGame($display);
$game->play($自分の手、$相手の手);

// 中国語
$display = new chineseDisplay();
$game = new jankenGame($display);
$game->play($自分の手、$相手の手);
```
```
// ポリモーフィズム
// 言語ごとにdisplayのクラスは異なる。同名のメソッドであるshowメソッドで見かけ上同じ処理をしているのに、処理結果が変わる）
// ある1つのメソッドの呼び出しに対して、クラスごとに異なる処理を行う = ポリモーフィズム
$this->display->show($result);
```
つまり、拡張(新しい言語を追加)する際
クライアント側の処理の追加、と新言語のクラスを定義すれば良く、プロバイダの修正が不要になる。

## 拡張に対して開いていて、修正に対して閉じている(オープンクローズド原則)
簡単に仕様を追加できるのに、それに関わる内部処理の修正が不要。

ところで。。。

jankenGameクラス内のresultがdisplayで使われてる→resultに関する修正がクラス外に影響してしまう可能性がある！！

解決するなら？

①$resultの値(勝ち、引き分け、負け)に対応する値を定数化しておく。

値に関する修正範囲は定数化している部分に限定されるため、これも修正範囲を限定する方法の1つ。

②ポリモーフィズムで解決する

- $resultの影響範囲をjankenGameクラス内に留める。

- $displayに3つのメソッドwin,draw,loseメソッドを定義。

- この3つのメソッドをjankenGame内の$resultの値によって場合分けして呼び出す。

### ポリモーフィズム以外にもオープンクローズド原則を実現する方法はある。状況によって適した判断を行うべき

## インターフェース
上記のdisplay系クラスにはルールがある。

それは、「必ずshowメソッドが定義されていなければならない」ということ。

〇〇Displayクラスは必ずshowメソッドが定義されていなければならない。

そのルールを開発者に伝えるのがインターフェース。
```
interface DisplayInterface {
    function show(int result);
}
```
これを利用して
```
class japaneseDisplay
{
    //これを定義した瞬間に、showメソッドを定義しないとvscodeやphpstormでエラーが出る
    implements DisplayInterface

    public function show($result)
    {
        $result=1なら勝利と出力
        $result=0なら引き分けと出力
        それ以外なら敗北と出力
    }
}
```
### インターフェースによって、開発者に実装のヒントを与える



## ポリモーフィズムまとめ

### __異なるクラスを同じものとしてあつかうことで__
### __クラスを修正せずとも拡張を可能にする__



# オブジェクト指向プログラミングとは？
- ## カプセル化とポリモーフィズムにより
- ## 堅牢で変化に柔軟に対応する
- ## 長寿命のソフトウェアを開発する手法