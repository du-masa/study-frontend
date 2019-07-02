# デベロッパーツールパフォーマンス計測周り

## Networkパネル

NetworkパネルではWebページでダウンロードされるリソースに関する情報収集を行えます。

デベロッパーツールを開いて、networkパネルを選択します。
その後リロードをすると、そのページでダウンロードされるリソースの一覧を確認することができます。

<img src="/image/developerTools/network.gif" width="700">

### サマリー

サマリーではnetworkパネルの全体数値を表示しています。

<img src="/image/developerTools/network_summary.png" width="700">

サマリーに表示されている内容は下記になります。

- リクエストの数
- ペイロードサイズ(ヘッターなどを除いた純粋なリソースサイズ)
- リソースのサイズ
- ページが表示されるまでの時間
- DOMContentLoadedイベントが着火するまでの時間
- Loadイベントが着火するまでの時間

※ DOMContentLoadedとLoadの違いは、DOMContentLoadedはDOMツリーの構築の完了、Loadは画像やすべてのスクリプトが読み込まれた時点で発生するイベントになります。

### リクエストテーブル

ダウンロードされたリソースはリクエストテーブルと呼ばれるテーブルに表示されます。

<img src="/image/developerTools/request_table.png" width="700">

ここに表示されているの内容は下記になります。

- Name: ファイルの名前
- Status: HTTPのステータスコード
- Type: ファイルタイプ
- Initiator: リソースが呼ばれているファイル名
- Size: ファイルサイズ
- Time: リクエストからダウンロードまでの時間
- Waterfall: Timeの詳細


#### リソースの詳細をみる

Nameをクリックするとリソースの詳細を見ることができます。
詳細は下記になります。

<img src="/image/developerTools/network_detail.gif" width="700">


- Headers リクエストヘッターとレスポンスヘッターの中身を確認できます。
- Preview: リソースを確認できます。
- Response: レスポンスボディの中身を確認できます。
- Timing: リクエストからダウンロードまでの時間を確認できます。リクエストパネルのWaterfallと同じ


#### テーブルのカラムを追加する

カラムタイトルを右クリックするとリクエストテーブルのカラムに追加できます。

<img src="/image/developerTools/network_add_column.gif" width="700">

#### リクエストからダウンロードまでの時間について

リクエストテーブルのWaterfallカラム、および詳細のTimingペインではリクエストからダウンロードまでの時間を確認できます。
ここを見ることでどのリソースがどれくらい時間をかけてダウンロードしているかを確認できます。

<img src="/image/developerTools/network_timing.png" width="700">


##### サーバとの接続をセットアップするフェーズ

- Queueing: ネットワーク処理のキューイングに要した時間
- Stalled: プロキシのネゴシエーションを含んだ、TCPの接続制限による接続待ちなどによって発生するリクエスト開始までの時間
- DNS Lookup: DNSルックアップに要した時間
- Initial Connection: SSLやTCPのネゴシエーションを含めた初期接続確立までの時間
- SSL: SSLのハンドシェイクに要した時間


HTTPのKeepaliveにより、一度TCP接続がされれば、一定時間は`DNS Lookup`,`Initial Connection`, `SSL`が行われず、すぐにリクエストが発生します。

<img src="/image/developerTools/network_timing2.png" width="700">


##### 実際にデータのやりとりを行うフェーズ
- Request sent: リクエストの送信に要した時間
- Waiting(TTFB): リクエストを送信してから最初のレスポンスを受信するまでの時間。TTFBはTime To First Byteの略。
- Content Download: サーバからのレスポンスデータを受信するのにかかった時間


また一番下には全体でかかったトータル時間が表示されます。


#### フィルターや検索で見たいリソースを探す


<img src="/image/developerTools/filter.png" width="20">アイコンを有効にすると下記のFiltersエリアが表示されます。

<img src="/image/developerTools/filters.png" width="700">

ここでは、文字列にリソースの検索やファイルタイプによるフィルタが行えます。

**検索**

<img src="/image/developerTools/network_search_text.gif" width="700">


**ファイルタイプのフィルタ**

<img src="/image/developerTools/network_search_filter.gif" width="700">


#### キャッシュを無視したリクエストを試す

`Disabled cache`を有効にするとブラウザキャッシュを無視してリクエストを行います。純粋なリクエストパフォーマンスを試す際に有効です。

<img src="/image/developerTools/network_disabled_cache.gif" width="700">


反対に`Disabled cache`を無効にするとキャッシュされているファイルには`Size`カラムが`"from memory(disk) cache"`が表示されます。

<img src="/image/developerTools/network_enable_cache.gif" width="700">


#### 前回のリソーステーブルを保持する

`Preserve log`を有効にすると、リロードやページ遷移を行なってもリソーステーブルの内容はリセットされず保持されます。

<img src="/image/developerTools/network_pre_log.gif" width="700">

#### ネットワークの状態に応じたリクエストを試す

ネット環境は常に快適な状態とは限りません。オフラインや３G回線時にどうなるかの確認を行う必要があります。

`Offline`のチェックボックスを有効にするとオフライン状態になります。通常のWebサイトではHTMLが取得できなくなり、ページが表示されません。Service WorkなどでCache APIを使用するとオフラインでもWebサイトにアクセスが可能になります。その場合の検証に使用できます。

<img src="/image/developerTools/network_offline.gif" width="700">

また、`Offline`チェックボックスの隣にセレクトボックスがあります。ここから３G回線での接続を試すことができます。

<img src="/image/developerTools/network_3g.gif" width="700">


## Performanceパネル

Performanceパネルではレンダリング処理のパフォーマンスを計測するのに役立ちます。時系列になっているので、どのタイミングでどんな処理がおこなれており、ボトルネックなっている箇所などを把握するのに役立ちます。

デベロッパーツールを開いて、Performanceパネルを選択します。

<img src="/image/developerTools/performance_start_btn.png" width="100">

左上にある`Record`ボタンを押すことで記録できます。
任意のタイミングで`Stop`ボタンを押すことで記録を終了します。


またその右の`Start profiling and reload page`ボタンを押すとページ読み込み時のパフォーマンスを記録できます。

<img src="/image/developerTools/performance.gif" width="700">

### 構造

Performanceは４つのペインに分かれています。

<img src="/image/developerTools/timeline-annotated.png" width="700">

1. コントロール:  記録を開始したり停止したりと各種制御を行えます。
2. 概要: ページのパフォーマンスの概要を示します。
3. フレームチャート　どのタイミングでどんなイベントが起きたかなど確認できます。
4. 詳細　フレームチャートで選択したイベントなどの詳細を確認することができます。

### 概要ペイン

概要ペインでは３つのグラフを見ることができます。

#### FPS

FPS(Frame Per Second)とは１秒間に何回画面が更新されるかの単位です。数値が多いほどアニメーションなど画面の切り替えによって表現されるものは滑らかな動きになります。Webにおいては60FPS(1秒間60回切り替える)が理想とされています。

概要のFPSでは、グラフが高いほどFPSの数値が高いということを示しています。

<img src="/image/developerTools/performance_fps.png" width="700">

#### CPU

CPUの使用率をグラフに表しています。グラフが高いほど使用率が高いことになります。

また、ここのグラフでいくつかの色が使われてますが、ブラウザのレンダリングフローにごとに色分けされてます。

- 青: Loading
- 黄: Scripting
- 紫: Rendering
- 緑: Painting
- 灰: Other


<img src="/image/developerTools/performance_cpu.png" width="700">


#### NET

ネットワークの通信状態を示しています。
濃い青色が優先度の高いリソース(HTML, CSS, JS)のダウンロード、薄い青色が優先度の低いリソース(画像など)になります。


<img src="/image/developerTools/performance_net.png" width="700">

#### 時間の指定

概要ペインをクリックすることで、見たい時間帯を絞り込んだり、反対に広げたりできます。

<img src="/image/developerTools/performance_timer.gif" width="700">

### フレームチャートペイン

フレームチャートペインでは時系列に沿ってより細かい詳細を見ることができます。いくつかのセクションに分かれています。

#### Network セクション

どのタイミングでどのファイルがダウンロードされているかを示しています。

ファイルの種類ごとに色分けされています。

- 青色: HTML
- 紫色: CSS
- 緑色: 画像
- 黄色: JavaScript
- 灰色: フォント

<img src="/image/developerTools/performance_network.png" width="400">

ネットワークの処理の状態は上記のように示しています。

- 左の直線: リクエストを送信するまでの時間(サーバーとの接続など)
- 薄い四角形: リクエストからTTFBまでの時間
- 濃い四角形: TFBからすべてのデータを受信するまでの時間
- 右の直線: メインスレッドがデータを処理するまでの時間

棒グラフの左上に付いている小さい四角は、優先度の高いリソース(濃い青色)かそうでないか(薄い青色)を示しています。

#### Main セクション

メインスレッドで実行されている処理を時系列に沿って確認することができます。

main セクションを選択すると、その下にある詳細ペインのsummaryに円グラフが表示されます。この円グラフを見ると、どのレンダリングフェーズに時間がかかっているかがわかります。

<img src="/image/developerTools/performance_main.gif" width="700">

<img src="/image/developerTools/performance_main_summary.png" width="400">


メインスレッドを時系列で追っていくと、各レンダリングフェーズがどのタイミングで行われているかがわかります。


<img src="/image/developerTools/performance_main_frame.gif" width="700">


#### ボトルネックを探す

Performanceパネルではボトルネックとなっている処理を発見しやすくなっています。

<img src="/image/developerTools/performance_ bottleneck.png" width="500">

処理が重い所にはフレームの右上に赤く三角マークが表示されます。

またフレームをクリックすると、詳細ペインに処理の詳細が表示されます。

`Bottom-Up` タブを選択すると各Activityが表示され、`Self Time`カラムの時間が高い処理を見つけることができます。
ファイルへのリンクもあり、問題のあるコードを見つけることが可能です。

<img src="/image/developerTools/performance_main_frame_summary.gif" width="700">


このコードは、10,000回for文を回す処理を行なっており、重たい処理となっていました。

### 詳細ペイン

詳細ペインではフレームチャートのより詳細を見ることができます。

- summary: 計測したい処理の実行時間、関数名、処理内容の分布を示す円グラフなどが表示されます。
- Bottom-Up: 実行時間とともに関数を表示するので、パフォーマンスに影響を与えている関数がどこに書かれていてどこから呼び出されているのかがわかります。
- Call Tree: 関数の呼び出し元からツリー状に呼び出しの全体像を辿っていくことができます。
- Event Log: 計測したタイミングから順おって処理が表示されます。Loading, Scripting, Rendering, Paintingなどのフェーズごとに絞り込んだ表示もできます。

<img src="/image/developerTools/performance_main_frame_summary2.gif" width="700">

### スクリーンショットをとる

時系列に沿ってどのタイミングでどんな画面表示がされているかを確認できます。

コントロールペインの`Screenshots`にチェックを入れると記録することができます。


<img src="/image/developerTools/performance_screenshots.gif" width="700">

### メモリー使用量を確認する

コントロールペインの`Memory`にチェックを入れると記録することができます。

<img src="/image/developerTools/performance_memory.gif" width="700">

青い線がJavaScriptの処理によって使われたメモリーの量を示しています。JavaScriptの処理が重いと高くなります。
また、メモリー使用量が自然と低下していかない場合はメモリリークの可能性がありますので、メモリーの使用量が上昇し続けている箇所で発生している処理を特定しコードの書き方に問題ないかを確認する必要があります。


## memoryパネル

memoryパネルではJavaScriptのオブジェクト単位でメモリーの使用量を確認することができます。

### スナップショットをとる

`Heap snapshot`を選択し、`Take snapshot`ボタンを押すとスナップショットを取ることができます。

<img src="/image/developerTools/memory_snapshot.gif" width="700">

取ったスナップショットは左上のドロップダウンから４つの表示モードを選択できます。

- Summary オブジェクトが占めるメモリの総容量をコンストラクタ名(クラス名)でグルーピングして表示する。

- Comparison スナップショットが複数ある場合に、選択中のスナップショットと他のスナップショットを比較する

- Containment グローバルに存在するオブジェクトをツリー状に表示する

- Statistics ヒープ領域全体を占めるJavaScriptの各データの円グラフ

### オブジェクトの一覧を確認する

スナップショットを取るとオブジェクトの一覧を取得できます。

一覧にはオブジェクト名(コンストラクタ名)の他に３つカラムがあります。

- Distance ルートのオブジェクトから数えた参照の数(階層の数の方がわかりやすい？)。値が大きいほど深い参照になる。

- Shallow Size オブジェクトそのもののメモリサイズ
- Retained Size オブジェクトの処理に必要なJavaScriptや参照している他のオブジェクトを含めた合計のメモリサイズ

### 参照しているオブジェクトを確認する

オブジェクト一覧の下部には、選択したオブジェクトがどのオブジェクトから参照されているかが確認できるようになっています。

<img src="/image/developerTools/memory_snapshot3.gif" width="700">


### 時系列でメモリーの状態を確認する

`Allocation instrumentation on timeline`を選択して、Take snapshot`ボタンを押すと、一定時間ごとにスナップショットを取ってくれます。

<img src="/image/developerTools/memory_snapshot4.gif" width="700">

グラフは青色のバーと灰色のバーの２種類存在し、青色は計測されたタイミングで確保されたメモリ量を示します、反対に灰色のバーは解放されたメモリを示します（GC）
