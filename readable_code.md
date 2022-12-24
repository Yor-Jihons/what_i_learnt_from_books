# リーダブルコード

※ 各章・各節のタイトルは当書からの引用である。

## Chapter 1. Code Should Be Easy to Understand

### What Makes Code "Better"? (「いい」コードとは? )

コード量が少ないと可読性が上がりやすいが、内容次第では逆に可読性が悪くなることがある。

当書では、``return exponent >= 0 ? mantissa * (1 << exponent) : mantissa / (1 << -exponent);``のような例が挙げられている。
行数としては一行なのでコード量は少ないが、初見ではわかりにくい。

そのため、適切にその都度「最善だと思われれるコード」を選択すべきである。

### The Fundamental Theorem of Readability ( 可読性の基本定理 )

> Code should be written to minimize the time it would take for someone else to understand it.
> [訳]コードは他者がすぐに理解できるように書くべきである

開発から保守まで一人で行うことは稀で、基本的には複数人でやることが多い。そのため、同じチームの人間が読んでもすぐに理解できるコードを書くべきである。
理解するのにかかる時間が短ければ短いほどいいとされている。個人開発の場合であっても六か月後の自分はもはや他人である。

### Is smaller Always Better? ( 短いコードは絶対にいいコードなのか? )

コードは基本的に短い(行数が少ない)方が望ましいが、必ずしも短いほどいいわけではない。
当書にあるサンプルとして``assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());``と``bucket = FindBucket(key); if (bucket != NULL) assert(!bucket->IsOccupied());`` で比較している。
前者では確かに行数は少ないが読んで理解するのにやや時間がかかる。しかし後者は行数としては多いが比較的理解しやすいものとなっている。
よって「必ず短い方がいい」とは言えないのだ。

### Does Time-Till-Understanding Conflict with Other Goals?

このリーダブルな発想は他のアーキテクチャやテスト技法といったものと相容れないかと思われることもあるが、むしろその逆でリーダブルなコードであればテストもしやすくなる。

### The hard Part

たとえ単独で開発していても他者が読むこと想定して名前を付けていくことが望ましい。

## Part One. Surface-Level Improvements

## Chapter 2. Packing Information into Names ( 名前に情報を詰める )

> Pack information into your names.
> [訳] 名前に情報を詰めよ

よくtmpというような曖昧な名前で変数名を付けたりするが、それは避けるべきである。

### Choose Specific Words ( ピンポイントな名前を選択せよ )

毎回getのような曖昧な名前を付けるのではなく、明確に名前からでもわかるような命名をすべきである。
たとえば``int id = store.getId();``としたとき、オブジェクトが持つデータを取り出しているのかDB等からのデータを取り出す操作なのか不明瞭である。
そのため、DBからデータを取ってくる場合はfetchのような具体的な名前にすべき。

``int size;``とした場合でも「容量」なのか「幅や高さ」といったものなのか「体重」のようなものなのかが不明瞭であるため、``int height;``といった名前にすべき。

#### Finding More "Colorful" Words ( 色のついた語を見つける )

プログラミング言語の多くは英語である。英語は多くの類義語があるためその中から最も適切な語を選ぶべきである。

> It's better to bo clear and precise than to be cute.
> [訳] 気取った語よりも明確な語の方が望ましい

たとえば毎回startを使うよりもlaunch, create, begin, open といった語の中から選んだ方が望ましい場合がある。

### Avoid Generic Names Like tmp and retval ( 汎用的な名前を避けよ )

tmpといった「どういうデータが入っているのかわからなくなるような汎用的な名前」を避けるべきである。
仮にそういう名前の変数を定義した場合、コードを読む際に脳に負荷がかかりやすくなる。
なので``int sum_of_sequence;``といった名前にすべきである。

> The name retval doesn't pack much information. Instead, use a name that describes the variable's value.
> [訳] retvalという名前は意味を持たない。代わりに、変数の値を意味するような名前を使え。

#### tmp

しかしtmpという名前は絶対に使わない方がいいかと言えば違う。
for文等のような数行程度のスコープでほぼ役割がない場合はtmpという名前でも許容される。

> The name tmp should be used only in cases when being short-lived and temporary is the most important fact about that variable.
> [訳] tmpという名前はスコープが小さく一時的なものであるべきだ。

#### Loop Iterators

for文で入れ子にした場合、i,j,k ... といった名前にすることが多い。
しかし、``if( club[i].members[k] == users[j] )``のようにすると取り間違えてしまう可能性があり、バグの温床になりやすい。  
そのため、``int mi, ci, ui;``といった「どの配列のためのものか」がわかるようにした方が望ましい。

#### The Verdict on Generic Names

上記のようにtmp等の曖昧な名前でも有用な場合もある。

> If you're going to use a generic name like tmp, it, or retval, have a good reason for doing so.
> [訳] 明確な理由がある場合はtmp, it, retval といった曖昧な名前を使える。

### Prefer Concrete Names over Abstract Names ( 抽象的な名前よりも具体的な名前を )

ServerCanStart() というような名前よりも CanListenOnPort() のような直接的な名前にすべきである。

> 例: DISALLOW_EVIL_CONSTRUCTORS

当書の例ではDISALLOW_EVIL_CONSTRUCTORSというマクロを定義して、コンストラクタやコピーコンストラクタを使えなくするというサンプルである。

しかし、DISALLOW_EVIL_CONSTRUCTORSという名前よりもDISALLOW_COPY_AND_ASIGNという名前の方が無難である。EVILの方だと「どういう内容なのか」が不明瞭だがCOPY_AND_ASIGNであれば「コピーとアサインを出来なくなくする」というのが明確になる。

> 例: --run_locally

当書ではコマンドライン引数として「デバッグ情報を表示する」という意味で使っているようだが、利用者からすると「なぜこのオプションが必要なのか」等がわかりづらい。ここで``--run_locally``を``--extra_logging``という名前であればイメージしやすい。
DBを利用するかどうかというオプションとしてであれば``--use_local_database``のような名前の方が理解しやすい。

### Attaching Extra Information to a Name

名前に詳細情報を付けるべき。``string id;``としていても16進数形式のIDであれば``string hex_id;``のような名前にすべき。

#### Values with Units

単位を名前につけるのも有用。たとえば時間測定の場合は``start``よりも``start_ms``(ミリ秒)とした方がわかりやすい。

#### Encoding Other Important Attributes

ネットワーク通信等で安全かどうかわからないURLを``untrustUrl``、安全なURLを``trustedUrl``のような名前でわかりやすくすべきである。パスワードを格納する変数についても```password``よりも``plaintext_password``のように具体的な名前にすべき。

### How Long Should a Name Be? ( 名前はどのぐらいの長さにすべきか )

名前はできる限り短いものにすべき。

#### Shorter Names Are Okay for Shorter Scope ( 狭いスコープのための短い名前はOK )

スコープが小さい場合は脳内のリソースは少なく済むため、``m``のような短い名前でもOK。
しかし関数呼び出し時に引数を戻り値扱いにする場合のようなときは適切な名前にすべき。

#### Typing Long Names - Not a Problem Anymore

長い名前であっても最近のエディタ( VSCode等 )を使えば完全補完機能があるため楽にコーデイングができる。
よって使えるものは使え。

#### Acronyms and Abbreviations ( 頭字語や省略語 )

頭字語(NATOやWHO等のような頭文字で構成されている語)や省略語はできる限り使うべきではない。
しかし``str``といったよく使われていて組んだ人に聞かなくともわかるようなものであればOK。

#### Throwing Out Unneeded Words ( 不要な語は捨て去るべき )

``ConvertToString()``のような名前だとConvertという部分を省略してもそんなに問題は無いので、``ToString()``とする。

### Use Name Formatting to Convey Meaning

クラス名はUpperCamelCase、(C言語での)マクロは```MACRO_NAME``のような名前といった具合に命名規則を定義して利用することで可読性が上がることもある。

#### Other Formatting Conventions

クラス名は大文字からはじめ、オブジェクト名は小文字から始める…といった命名規則を使うのも手。
どういう命名規則にするかは組む人やチームによるが、プロジェクト内では統一すべき。

## Chapter 3. Names That Can't Be Misconstrued ( 誤解されない名前 )

名前を付けるときは誤解されない語を使うべき。

> Actively scrutinize your names by asking yourself, "What other meanings could someone interpret from this name?"
> [訳] 自発的に「他の人が読んでこの名前から別の意図を解釈されないか」を自問自答していけ

### Example: Filter()

``results = Database.all_objects.filter("year <= 2011")``とした場合、resultsに入っている値がどうなっているか判断がつきづらい。
「yearは2011を含んでいる」可能性とそうでない可能性の二通りが考えられるため。
``year <= 2011``を満たしているものだけを抽出している場合は``select()``という名前で、除外する場合は``exlude()``のような名前にすると可読性が上がりやすい。

### Example: Clip(text, length)

``def Clip(text, length):``とした場合、「後ろからlength分、削除する」とも読めるし「length分切り捨てる」とも読める。データをlength分切り捨てる場合は``Trancate(text, length)``の方が合う。
また、lengthという引数名も「バイト数」なのか「文字数」なのか「単語数」なのかがわかりにくい。
そのため``length``は``number_of_bytes``のように明確にしたほうがいい。

### Prefer min and max for (Inclusive) Limits

``CART_TOO_BIG_LIMIT = 10``とした場合、``if shopping_cart.num_items() >= CART_TOO_BIG_LIMIT``といったようにCART_TOO_BIG_LIMITを含むかどうかのバグを含みやすい。そこで``MAX_ITEMS_IN_CART``とするとそのバグも見つけやすくなる。

> The clearest way to name a limit is to put max_ or min_ in front of the thing being limited.
> [訳] 範囲を明確にする名前を付けるには max_ か min_ をその名前の前に付ける。

### Prefer first and last for Inclusive Ranges

(指定の末尾を含めた)配列の範囲を表す場合はstart, end よりも first, last のようなペアで付けた方がいい。

### Prefer begin and end for Inclusive/Exclusive Ranges

たとえば「今月末までのイベント」のようなendを含める・含めないの基準がある場合はbegin/endの方がいい。

### Naming Booleans

``bool read_password = true;``という変数名だと「読むべきパスワード」なのか「すでに読んだパスワード」なのか判別しづらい。そこで``need_password``のように``read``という単語を避けて付けるといい。
ただし、``disable_ssl``のような否定形の単語を使うのは避けるべき。

### Matching Expectations of Users

ユーザが解釈ミスを起こしかねないような単語を使うべきではない。

#### Example: get*()

よくget～と使うがそれはオブジェクトが管理するデータを取得するだけの処理なので、もしDBから取ってくるといった重たい処理をする場合は``fetch``というようなピンポイントな名前にすべき。

#### Example: list::size()

C++のlist::size()はその都度計算しているらしく、get*()と同様の問題を抱えている。

### Example: Evaluating Multiple Name Candidates

???


## Chapter 4. Aesthetics

### Why Do Aesthetics Matter?

ここでは「インデントがぐちゃぐちゃ」だったりと**単に動けばいい**というコードと意味ごとに整列させたりしたコードを比較して、どちらがいいかをサンプルとしてる。

### Rearrange Line Breaks to Be Consistent and Compact

```Java
public class PerformanceTester {
 public static final TcpConnectionSimulator wifi = new TcpConnectionSimulator(
  500, /* Kbps */
  80, /* millisecs latency */
  200, /* jitter */
  1 /* packet loss % */);
public static final TcpConnectionSimulator t3_fiber =
 new TcpConnectionSimulator(
  45000, /* Kbps */
  10, /* millisecs latency */
  0, /* jitter */
  0 /* packet loss % */);
public static final TcpConnectionSimulator cell = new TcpConnectionSimulator(
 100, /* Kbps */
 400, /* millisecs latency */
 250, /* jitter */
 5 /* packet loss % */);
}
```

のようなコードよりも

```Java
public class PerformanceTester {
 public static final TcpConnectionSimulator wifi =
  new TcpConnectionSimulator(
   500, /* Kbps */
   80, /* millisecs latency */
   200, /* jitter */
   1 /* packet loss % */);
 public static final TcpConnectionSimulator t3_fiber =
  new TcpConnectionSimulator(
   45000, /* Kbps */
   10, /* millisecs latency */
   0, /* jitter */
   0 /* packet loss % */);
 public static final TcpConnectionSimulator cell =
  new TcpConnectionSimulator(
   100, /* Kbps */
   400, /* millisecs latency */
   250, /* jitter */
   5 /* packet loss % */);
}
```

のように揃えると可読性が上がりやすい。
もしくは

```Java
public class PerformanceTester {
 // TcpConnectionSimulator(throughput, latency, jitter, packet_loss)
 // [Kbps] [ms] [ms] [percent]
 public static final TcpConnectionSimulator wifi =
  new TcpConnectionSimulator(500, 80, 200, 1);
 public static final TcpConnectionSimulator t3_fiber =
  new TcpConnectionSimulator(45000, 10, 0, 0);
 public static final TcpConnectionSimulator cell =
  new TcpConnectionSimulator(100, 400, 250, 5);
}
```

のようにしてもいい。

### Use Methods to Clean Up Irregularity

テスト時でも関数化するなりして可読性を上げるべき。

### Use Column Alignment When Helpful

```C
CheckFullName("Doug Adams" , "Mr. Douglas Adams"  , "");
CheckFullName(" Jake Brown ", "Mr. Jake Brown III", "");
CheckFullName("No Such Guy" , ""                  , "no match found");
CheckFullName("John"        , ""                  , "more than one result");
```

のように行や列で揃えたりしても可読性が上がる。

人によっては好ましくないと感じるらしいが、そこまで問題とはいえない。

### Pick a Meaningful Order, and Use It Consistently

C言語でいう構造体を使う場合のように順番そのものは流れに寄与しないような設定等でも**統一すべき**らしい。
たとえばWindows APIでのウィンドウクラスの設定時のようなものだ。

アイコン設定 → カーソル設定 → ... という並びならそれを完全固定する。
ある関数内では a → b → c で、ある関数内では b → a → c のような並びだとわかりにくくなる。
そのため常に統一させるべき。

### Organize Declarations into Blocks

クラス定義のような場合はコンストラクタ群、ヘルパー群、getter/setter系群...というように種類ごとに書くとわかりやすいらしい。

```C++
class Test{
    public:
        // コンストラクタ群
        Test();
        ~Test();

        // ヘルパー
        static void make_Test();

        // getter/setter
        int getId( void ) const;
        //...
};
```

### Break Code into "Paragraphs"

「処理Aをする」「処理Bをする」といった段落を作ると可読性があがる。

### Personal Style versus Consistency

```C++
class Test{
...
};
```

```C++
class Test
{
  ...
};
```

上の二つのコードはどちらでも好きな方(規約に違反しない限り)でいいが、常に統一させること。

> Consistent sytle is more important than the "right" style.
> [訳] 常に同じスタイルでやることは「正しいスタイル」よりも重要である。

## Chapter 5. Knowing What to Comment

> THe purpose of commenting is to help the reader know as much as the writer did.
> [訳] コメントを書く目的は書いた人と同じレベルで理解できるようにすることだ。

### What NOT to Comment

関数名やメソッド名等のような読んでわかるものにはコメント文は不要。

> Don't comment on facts that can be derived quickly from the code itself.
> [訳] コードを読んですぐにわかることはコメントにするな。

#### Don't Comment Just for the Sake of Commenting

不要なコメントはするな。

#### Don't Comment Bad Names - Fix the Names Instead

コメントにするぐらいなら名前を変えろ。

### Recoding Your Thoughts

できる限りコメントにすべきではないが、コメントを付けるのなら『書いた人の考え』がわかるようにすべき。

#### Include "Director Commentary"

いわゆるディレクターコメンタリーと呼ばれる、映画についているもののようなものもいい。

#### Comment the Flaws in Your Code

"ToDo:"や"FixMe:"といった一時的な情報も有用。

よく使われるもの

| Marker | 意味 |
| ---- | ---- |
| TODO | まだやっていないこと |
| FIXME | 問題点がここにあることを示す |
| HACK | 未確定の解決策 |
| XXX| 危険な問題があることを示す |

#### Comment on Your Constants

```Python
NUM_THREADS = 8 # as long as it's >= 2 * num_processors, that's good enough.
```

といったように定数にもコメントを付けると可読性が上がる。

### Put Yourself in the Reader's Shoes

読む人の立場になって考えてみるとわかるかもしれない…。

#### Anticipating Likely Questions

読む人が感じる可能性がある疑問を先回りしてコメントにすべし。

#### Advertising Likely Pitfalls

> 関数やクラスをドキュメント化する際に「このコードで不思議に思うことはないか? 間違って使われないか?」と自問自答することが良い質問である。

```C
// Calls an external service to deliver email. (Times out after 1 minute.)
void SendEmail(string to, string subject, string body);
```

のようにユーザが陥りそうな穴を先に指摘しておくのもベター。

#### "Big Picture" Comments

概要を書くべき。ドキュメント化を恐れるな。

#### Summary Comments

段落ごとに概要を書け。

### Final Thoughts - Getting Over Writer's Block

コメントは面倒臭がらずに書くべき。
