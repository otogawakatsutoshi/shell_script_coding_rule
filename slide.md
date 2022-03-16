# シニアエンジニアのためのコーディング規約

## 


## 何のためのコーディング規約?

1. メンバーの勉強コストを減らす。
2. トラブル解決を早くするため
3. コミュニケーションコストを減らす。

## 方針

1. 明示しろ考えさせるな
2. awkやループを使うな

## 明示しろ。考えさせるな

1. 突然勉強会が始まる
2. トラブル解決を早くする、減らすため

## awkやループをあまり使うな

1. デバッグが難し過ぎる
2. 再利用可能な部品として分けるのが難しい

### 再利用可能な部品として分けるのが難しい

再利用性ってなんだろうって考えた時に次のことできたら最高

1. 考えずに
2. 関数に切り出せる
3. 対話形式で実行できる
4. 変数名や、オプションなど最低限のものだけ変更

## じゃあどういうコードが悪いかと言うと


例えば、
df -h実行して、

このUSEDが指定の値以上の行だけ、表示するコマンドを
作りたいとしましょう。

名前はdisk_usageという名前のコマンド

1. set -e

基本的にsetでbashの設定いじるな。

よくあるのが、
とりあえずset -eとかset -eu
とかやっているの

設定とソースコードを両方見て初めて、
どこどこから

エラーでどっかに飛ぶというのは一種のgoto文なので、
合理的なルールやら、直感的にそうに決まっているがないと
混乱する。

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