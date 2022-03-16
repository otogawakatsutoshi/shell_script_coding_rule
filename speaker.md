
# シニアエンジニアのためのコーディング規約

前置きすると、
今回説明するのは、ファイルの最後に改行入れるとか、タブはスペース4個分とか。
一般的な意味のコーディング規約という意味じゃないです。

## 何のためのコーディング規約?

1. メンバーの勉強コストを減らす
2. トラブル解決を早くするため

これらの問題をソースコードに吸収して解決する切り口、
にコーディング規約をもっと使っていったらいいんじゃないかという話です。

## 方針

1. 明示しろ考えさせるな
2. awkやループを使うな

方針としてこの二つを取ったほうがいいのでないか？
と自分は考えています。
ここら辺知ってるわ！とかいう人は軽く聞き流していただけら。

### 明示しろ。考えさせるな

1. 突然勉強会が始まる

わかりづらい書き方や曖昧な書き方をすると、
他の人が読めないか、これってどういう意味とか
そこで勉強会が始まるんですよ。
これ本当に良くないと思ってて、簡単に一子相伝の
ソースコードになってしまう。

特に速度的な差もないし、修正のしやすさも変わらないなら、
明示して曖昧な書き方をなくしたほうがいい。

2. トラブル解決を早くするため

bashというか、バッチ処理は全てが
ビジネスロジックか、こけるとまずい処理なので、
できるだけ多くのエンジニアが読めたほうがいい。

エンジニアが業務理解できていても、
bashがわかりづらい書き方してるから読めません。だとそれで
そのbash書いた人連れてこないときついんですよ。
これは業務的に見てもかなり危険なので、この状態作りたくない。

### 要点として

この問題二つとも結局はソースコードによってコミュニケーションコスト
減らそうぜ！という話で、

できるだけ、エンジニアに業務理解や、事業としてどうしたいかと
今のシステム的にどうなのか？がどれぐらいかけ離れているかの理解に頭使って欲しい。

## awkやループ使うな

1. デバッグが難し過ぎる

ループって基本的にデバッグ難しいんですよ。
ループの中に複数の意味を持った処理が入りがちになるのに、
その処理だけ切り離して、実行してみるができないし、
ループ内の他の処理の影響をモロに受けるから。

Javaでいうとeclipseのデバッガ使ってループ内のバグ直すために
ステップ実行使って値確認してっていうそういう手段取れないときつい。

awkも同じ理由で結局はループだから。

2. 再利用可能な部品として分けるのが難しい

1. 考えずに
2. 関数に切り出せる
3. 対話形式で実行できる
4. 変数名や、オプションなど最低限のものだけ変更

たいていの場合は、コマンドとパイプの組み合わせで、
ループ処理が隠蔽できるから、書かなくていいはず。

特に必要がない限り、ループは使うべきでない。
go言語使ってて、なおかつ速度とメモリの使用量とかが過酷なものが求められるなら、
ループの中に全部入れないといけないとかは普通にありますが、

なぜループを使うかというと、これとifやcaseみたいな条件文と
一緒に描く時があって、特定の値の時に違う処理を書きたいから。

再利用性ってなんだろうって考えた時に次のことできたら最高

何々さんこれちょっとやってもらっていい？
50行目から60業目のやつ参考になると思うから
コピペして、コマンド変えてちょっとやってみて。
わからんかったら聞いて。

ビジネスでよく横展開できるかっていうけど、これも小さい意味では
横展開だと思ってて、これをできるだけ増やしたい。

たいていの場合は、コマンドとパイプの組み合わせで、
ループ処理が隠蔽できるから、書かなくていいはず。

## じゃあどういうコードが悪いかと言うと


例えば、
df -h実行して、

このUSEDが指定の値以上の行だけ、表示するコマンドを
作りたいとしましょう。

名前はdisk_usageという名前のコマンド

これ本に似たようなソースコード書かれているやつあったんですが、
下の本はこれよりもひどいです。

1. set -e

基本的にsetでbashの設定いじるな。

よくあるのが、
とりあえずset -eとかset -eu
とかやっているの

たいていのプログラムはコマンド実行前に失敗するかどうか明確に決まっているか、
強引にエラーが起きうる場所を明示させている。

Java,csharpだったら、try catchしてくれないとビルドの時エラーになるし、
pythonだったら、それよりもルール緩いから、実行時エラーにはならないけど、
公式の例とかみるとみんなtry catch書いてある。

これのおかげで、失敗する可能性がある処理が
誰が見てもわかるようになっているっていうメリットがある。

割と多いんですが、これパッと見た時に
どこがエラー起きる可能性があるのか？とか、
デバッグするならどの時点でのどの変数の値を見ればいいのかとか
わからないと思います。

特にbashはバックアップ取ったり、データの同期取ったり、
osの統計情報の監視とか。
割と失敗したらまあまあやばいこと、
雑にエラー起きたらそこで終了と書かないほうがいいし、
元のbashの動きを変更することはしないほうがいい。

一応set -eを使ったほうがいいパターンもあって、
世の中には読めるけど、人間が触っちゃダメなファイルってあると思うんですよ。
bashから設定ファイルを生成するとかそういうソースコードの場合。
一番わかりやすい例がgrubの設定で、
bashで書いてコマンド実行して設定ファイルを作っているんですよ。
grubで設定ファイルを生成するときに、異常がある設定ファイルで上書き
しない作りにしたいから。

見せると
このbashのスクリプト自体が自動生成されているから、人が直接
触ることないんですよ。
grub自体にバグがあって、自動生成されているスクリプトがおかしいときは
何がなんでも弾きたい。
そういう作りにするんなら、エラーは全部異常終了にしたほうがいいよね？
という考え方です。

たいていのバッチ処理は人間が触ること前提だし、エラー起きても、
エラー以外のところは処理したいとか普通にあるから、
大した理由ないのにいじるべきでない。

set -eもあまり良く無いんですけど、
これよりもっと良く無い
set -euってのがあって、
変数の状態によって、エラーの分岐がある
ということを明示できないんですよ。

環境変数をちゃんと削除することも考えている。
undeclare VARIABLE



2. オプションを必要無いのに変数に分けない

これを見た瞬間にオプションを動的に追加したり、減らしたりするのかな？
もしくは今後そういう処理が追加される予定があるのか？
という疑問が生まれるんですよ。

動的にしたいとしても、
例えば、rsyncでdry-runしたい場合とか、

if [ -z $frewr ]; then
    dry_run="--dry-run"
fi

rsync -av ./ /path/to/disk $dry_run
一部追加するとかそういうぐらいで、


3. ループ使わない

なんでこの人
なんでawk使わないか?
というとこれは
厳密にはスペース区切りのデータじゃないので、awkで綺麗に分割できない。
Mounted on
で一つのカラムだから、カラムにスペース入ってくると面倒臭いことになる。


## じゃあお前の言う書き方

走りになるんですけど、

## 

## 結論

1. 明示しろ考えさせるな
2. awkやループをあまり使うな

ここら辺のコストを運用エンジニアや、後輩が背負うことになるから。
ただ、ソースコード書けました!っていうレベルじゃないし、
もっと他のメンバーがソースコード書けないのは自分のせいにしたほうがいいという話です。

ループを入れるとループ全体としてどうか？
を追わないといけないけど、

パイプで繋げていった場合はコマンドの意味を追っていったら、
必ず、やりたいことが出るようになっている。

一定以上難しい、
愚直に一回でやろうとするとループが発生するもので
ループを避けるコツとしては
検索ロジックと、
結論や表示のロジックを分けることです。