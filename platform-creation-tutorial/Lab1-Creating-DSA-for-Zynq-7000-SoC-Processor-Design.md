
<table style="width:100%">
  <tr>

<th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>演習: SDSoC プラットフォームの作成</h2>
</th>

  </tr>
  <tr>
    <td width="17%" align="center"><a href="README.md">概要</a></td>
    <td width="16%" align="center">演習1: Zynq-7000 SoC プロセッサ デザイン用の DSA の作成</td>
    <td width="17%" align="center"><a href="Lab2-Creating-Software-Components.md">演習 2: プラットフォーム用ソフトウェア コンポーネントの作成</a></td>
    <td width="17%" align="center"><a href="Lab3-Creating-Custom-Platform-Using-the-SDx-IDE.md">演習 3: SDx IDE を使用したカスタム プラットフォームの作成</a></td>
  </tr>
</table>

## 演習 1: Zynq-7000 SoC プロセッサ デザイン用の DSA の作成  

この演習では、Vivado&reg; を使用して Arm&reg; Cortex&trade;-A9 プロセッシング システム (PS) およびプログラマブル ロジック (PL) 領域の IP ブロックを含んだ Zynq&reg;-7000 SoC プロセッサ デザインを作成します。Clocking Wizard IP を使用し、最終的なデザインで使用される可能性のある複数のクロックを生成します。複数の Processor System Reset IP ブロックを追加して、さまざまなクロック ソースのリセットを同期します。PL IP ブロックで生成される割り込みのための PS ブロックへのパスも含めます。sds++ システム コンパイラで使用するハードウェア インターフェイスを宣言し、ハードウェア アクセラレータを接続できるようにします。これでハードウェア デザインおよびそのインターフェイス メタデータがデバイス サポート アーカイブ (DSA) ファイルに含まれ、後でカスタム SDSoC プラットフォームを作成する際に使用できるようになります (<a href="Lab3-Creating-Custom-Platform-Using-the-SDx-IDE.md">演習 3: SDx IDE を使用したカスタム プラットフォームの作成</a>を参照)。

Vivado Design Suite IDE の基本的な情報は、『Vivado Design Suite ユーザー ガイド: 概要』 ([UG910](https://japan.xilinx.com/support/documentation/sw_manuals_j/xilinx2018_2/ug910-vivado-getting-started.pdf)) を参照してください。

<details>
<summary><big><strong>手順 1: Vivado IDE の起動とプロジェクトの作成</strong></big></summary>

#### Linux ホスト マシン

シェル プロンプトに次のコマンドを入力します。
   
   1. `source <Xilinx_Install_Directory>/SDx/<Version>/settings64.{sh,csh}`
   2. `vivado`
    
1 つ目のコマンドで Vivado を起動する前に環境変数を設定し、2 つ目のコマンドで Vivado を起動しています。 

#### Windows ホスト マシン

Windows ホスト マシンの場合は、次のいずれかの方法で Vivado を起動します。

   - Vivado デスクトップ アイコンをクリックします。

   - [スタート] メニューから [Xilinx Design Tools] → [Vivado 2018.2] → [Vivado 2018.2] をクリックします。

   - コマンド プロンプト ウィンドウで次のコマンドを入力します。
   
      1. `<Xilinx_Install_Directory>/SDx/<Version>/settings64.bat`
      2. `vivado`
    
     1 つ目のコマンドでは、Vivado を起動する前に環境変数を設定しており、2 つ目のコマンドでは Vivado を起動しています。 

#### Vivado プロジェクトの作成

Vivado プロジェクトを作成する手順は、次のとおりです。

1. 次の図に示す Getting Started ページの [Quick Start] セクションで [Create Project] をクリックします。

    ![](./images/image3.png)

2. New Project ウィザードが開きます。[Next] をクリックします。

    ![](./images/image4.png)
    
   [Project Name] ページが開きます。

3. プロジェクト名を **zynq7_board** に指定します。

4. [Project Location] にダイアログ ボックスに **/tmp** と入力します。

5. [Create project subdirectory] をオンにします。

   ![](./images/image5.png)

6. [Next] をクリックします。[Project Type] ページが開きます。

7. [RTL Project] をオンにします。

    ![](./images/image6.png)

8. [Next] をクリックします。[Add Sources] ページが開きます。

7. [Target language] ドロップダウン リストから [Verilog] を選択します。
8. [Simulator language] ドロップダウン リストから [Mixed] を選択します。

    ![](./images/image7.png)

9. [Next] をクリックします。[Add Constraints] ページが開きます。

    ![](./images/image8.png)

10. [Next] をクリックします。[Default Part] ページが開きます。

11. 使用可能なボードのリストから [ZYNQ-7 ZC702 Evaluation Board] を選択します。

    >**:information_source: ヒント:**
    >検索機能を使用すると、ZC702 をフィルターできます。
    
     ![](./images/image9.png)

12. [Next] をクリックします。

    >**:warning: 警告:**
    >Vivado では、複数のバージョンのボードがサポートされます。正しいボードをターゲットにしていることを確認してください。

13. [New Project Summary] ページでプロジェクトのサマリを確認し、[Finish] をクリックしてプロジェクトを作成します。

    ![](./images/image10.png)

</details>

<details>
<summary><big><strong>手順 2: IP インテグレーター デザインの作成</strong></big></summary>

1. Flow Navigator で [IP INTEGRATOR] を展開表示し、[Create Block Design] をクリックします。
   [Create Block Design] ダイアログ ボックスが開きます。

   ![](./images/image11.png)

2. IP サブシステム デザインの名前を指定します。この例の場合は、**zynq7_board** と指定します。

   >:pushpin: **注記:**
   >[Directory] および [Specify source set] ドロップダウン リストのデフォルト値は変更しないでください。

    ![](./images/image12.png)

    >**:information_source: ヒント:**
    >Vivado プロジェクトに複数のブロック デザインが含まれている場合、SDSoC プラットフォーム デザインを含む IP インテグレーター ブロック デザインの名前は SDSoC プラットフォームと同じ名前にする必要があります。それ以外の場合は、IP インテグレーター ブロック デザインの名前を Vivado プロジェクトや SDSoC プロジェクトと同じにする必要はありません。

3.  [OK] をクリックします。

#### ブロック デザインへの IP の追加

これで複数の IP ブロックを IP インテグレーター デザインに追加できるようになりました。次の表では、追加する IP ブロックと、それらの SDSoC での役割についてまとめています。
<table width="100%">
<tr>
<th>IP ブロック</th><th>の役割</th>
</tr>
<tr>
<td>Zynq-7000 SoC Processor System (PS)</td>
<td>

- キャッシュ階層を含むデュアル コア Arm プロセッサ

- 統合 I/O ペリフェラル

- 外部メモリ インターフェイスを使用した DDR メモリ コントローラー

- PS からプログラマブル ロジック (PL) へのインターコネクト

- PL から PS へのインターコネクト
</td>
</tr>
<tr>
<td>
Processor System Reset ブロック (PL)
</td>
<td>

- PL ロジックのリセット シーケンスおよび同期ブロック

</td>
</tr>
<tr>
<td>
Clocking Wizard (PL)
</td>
<td>

- PL ロジックを駆動する複数の出力クロック ジェネレーター
</td>
</tr>
<tr>
<td>
Concat ブロック (PL)
</td>
<td>

- Zynq-7000 SoC PS 割り込みリクエスト入力に使用される PL 割り込み構造
</td>
</tr>
</table>
<!-- end list -->

1. ブロック デザイン キャンバスで右クリックし、[Add IP] をクリックします。

    または、IP インテグレーター キャンバスの [Add IP] ボタン (**+**) をクリックします。

    ![](/images/image13.png)

2. IP カタログの [Search] ダイアログ ボックスが表示されます。[Search] フィールドに「zynq」と入力して ZYNQ7 Processing System IP を検索します。

   ![](./images/image14.png)

3. [ZYNQ7 Processing System] を選択し、<kbd>Enter</kbd> キーを押してデザインに IP を追加します。

   IP ブロックをダブルクリックしても IP インテグレーター キャンバスに追加できます。

   ![](./images/image15.png)

   ZYNQ7 Processing System が IP インテグレーター キャンバスに追加され、[Tcl Console] ウィンドウにも IP を追加するのに使用したコマンドが表示されます。

   `create_bd_cell -type ip -vlnv xilinx.com:ip:processing_system7:5.5 processing_system7_0`

   >**:pushpin: 注記:**
   >IP インテグレーター ブロック デザインで実行するほとんどの操作に、対応する Tcl コマンドがあります。 
   Tcl コマンドの詳細は、『Vivado Design Suite: Tcl コマンド リファレンス ガイド』 ([UG835]( https://japan.xilinx.com/support/documentation/sw_manuals_j/xilinx2018_2/ug835-vivado-tcl-commands.pdf)) を参照してください。


4.  IP インテグレーターで [Run Block Automation] リンクをクリックします。

    ![](./images/image16.png)

    次の図のように [Run Block Automation] ダイアログ ボックスが開きます。このダイアログ ボックスは、Zynq-7000 SoC IP コア用に FIXED_IO および DDR インターフェイスが作成されることを示しています。[Apply Board Preset] がデフォルトでオンになっているでの、定義済みのボードの利点を生かして PS がコンフィギュレーションされます。

   ![](./images/image17.png)

5.  [OK] をクリックして ZC702 ボードのデフォルト設定を使用します。

    IP インテグレーターの図は、Zynq-7000 SoC プロセッサでブロック オートメーションを実行するとアップデートされます。

    ![](./images/image18.png)

6.  IP インテグレーターの図を右クリックし [Add IP] をクリックして PL にペリフェラルを追加します。

7.  [Search] フィールドに「proc sys res」と入力して Processor System Reset を検索し、<kbd>Enter</kbd> キーを押してデザインに追加します。
    Processor System Reset IP ブロックにより、宣言したプラットフォーム クロックごとに同期リセット信号が作成されます。各リセットは、Clocking Wizard で生成されたクロックに関連付けられます。

    ![](./images/image19.png)

8.  合計 4 つのリセット IP ブロックが必要なので、上記 2 つの手順を繰り返し、Processor System Reset IP のインスタンスをさらに 3 つ追加します。または、Processor System Reset ブロックを複数回コピーして貼り付けます。

9.  **Clocking Wizard** IP ブロックを追加して、プラットフォームに PL クロックを提供します。クロック設定は、この後の手順でカスタマイズします。

    ![](./images/image20.png)

10. **Concat** IP を追加して、PL で生成された割り込みを PS ブロックに接続します。

    ![](./images/image21.png)

    ブロック図は、次のようになるはずです。IP の相対的な位置は異なる場合があります。

    >**:information_source: ヒント:**
    >IP インテグレーターの図は [Zoom In] および [Zoom Out] ボタンを使用するか、該当するキーボード ショートカット (拡大は **Ctrl**+**=**、縮小は **Ctrl**+**-**) を使用すると拡大および縮小できます。

    ![](./images/image22.png)

#### IP の再カスタマイズ

1.  ZYNQ7 Processing System IP ブロックをダブルクリックして、カスタマイズ ウィザードを開きます。

    ![](./images/image23.png)

2.  [Re-customize IP] ダイアログ ボックスでデフォルトのプロセッサ設定を次のように変更します。

    1.  Page Navigator で [PS-PL Configuration] をクリックします。

    2.  [AXI Non Secure Enablement] を展開します。

    3.  [GP Master AXI Interface] を展開します。

    4.  [M AXI GP0 interface] チェック ボックスをオフにします。

        ![](./images/image24.png)
        
    これにより、M_AXI_GP0 と M_AXI_GP1 PS インターフェイスの両方がハードウェア アクセラレータの接続ポイントとして使用できるようになります。デフォルトの PS 設定では、M_AXI_GP0 インターフェイスが PS での使用のために予約されています。オフにすると、このインターフェイスが空いて、SDx ツールで使用できるようになります。     

3.  Page Navigator で次を実行します。 

    1.  [Interrupts] をクリックします。

    2.  [Fabric Interrupts] チェック ボックスをオンにします。

    3.  [Fabric Interrupts] を展開します。

    4.  [PL-PS Interrupts Ports] を展開します。
    
    5.  [IRQ_F2P[15:0]] チェック ボックスをオンにします。

        ![](./images/image25.png)
    
        これで、最大 16 個の PL 割り込みが PS 割り込み入力ポートで処理できるようになりました。オンにすると、PS-PL 割り込みインターフェイスがイネーブルになり、PL Concat IP ブロックの片側で使用されます。Concat IP ブロックの反対側は sds++ システム コンパイラで使用できるように宣言され、PS への PL 割り込みが必要に応じて配線されます。

    6.  [OK] をクリックします。

4.  Clocking Wizard IP をダブルクリックして再カスタマイズします。

5.  [Re-customize IP] ダイアログ ボックスの [Clocking Options] タブで、[Primary Clock] の [Input Frequency] チェック ボックスをオンにし、値を 50.000 に変更します。PS では 50 MHz のクロックが生成され、Clocking Wizard IP への入力に使用されます。

7.  [Output Clocks] タブをクリックし、**clk_out2**、**clk_out3**、および **clk_out4** 出力クロックのチェック ボックスをオンにして追加します。[Output Freq (MHz)] の [Requested] 列の下の出力周波数を次のように設定します。

    - clk_out1 -\> 100.000

    - clk_out2 -\> 142.000

    - clk_out3 -\> 166.000

    - clk_out4 -\> 200.000

    PL 領域にさまざまな周波数を追加しておくと、ベース プラットフォームの柔軟性が増します。複数のクロック ソースがプラットフォームから使用できると、ベース プラットフォームを定義し直さなくても SDx ハードウェア アクセラレータで入力クロック ソースが変更できるようになります。

8.  [Output Clocks] タブでスクロール ダウンし、次の図に示すように [Reset Type] の [Active Low] をオンにします。

    ![](./images/image26.png)

9.  [OK] をクリックして Concat IP の [Re-customize IP] ダイアログ ボックスを閉じます。

10. **Concat** IP ブロックをダブルクリックして [Re-Customize IP] ダイアログ ボックスを開きます。sds++ システム コンパイラは PL 割り込みを Concat IP ブロックを介して PS ブロックに配線します。

11. 次のように [Number of Ports] フィールドを 1 に変更します。

    これにより、ほかの目的に予約されているものがなくなるので、sds++ システム コンパイラで Concat IP を介した PS ブロックへの配線に最大数の PL 割り込みを使用できるようになります。

    ![](./images/image27.png)

13. [OK] をクリックして Concat IP の [Re-customize IP] ダイアログ ボックスを閉じます。

    IP インテグレーター ブロック図は次のようになります。

    ![](./images/image28.png)

#### 設計アシスタンスの使用

設計アシスタンスを使用すると、Clocking Wizard と Processor System Reset ブロックを自動的に Zynq-7000 SoC プロセッシング システムに接続できます。

1.  [Run Connection Automation] をクリックします。[Run Connection Automation] ダイアログ ボックスが開きます。

    ![](./images/image29.png)

2.  次の図に示すように IP のクロック オプションを設定します。コネクション オートメーションを実行する各インターフェイスを選択すると、右側に選択したインターフェイスの説明とオプションが表示されます。

    ![](./images/image30.png)

3.  オートメーション オプションが次のように設定されていることを確認します。

    <table>
    <thead>
    <tr class="header">
    <th><strong>接続</strong></th>
    <th><strong>説明</strong></th>
    <th><strong>設定</strong></th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td><strong>clk_wiz_0</strong><br />
    - clk_in1</td>
    <td>Clocking Wizard の入力クロック。</td>
    <td><em><strong>/processing_system7_0/FCLK_CLK0 (50 MHz</strong></em>) が [Clock Source] オプションのデフォルトとして選択されます。デフォルト値のままにします。</td>
    </tr>
    <tr class="even">
    <td><strong>proc_sys_reset_0<br />
    </strong>- slowest_sync_clk</td>
    <td>このリセットが同期されるクロック ソース。</td>
    <td>[Clock Source] オプションのドロップダウン リストから <strong>/clk_wiz_0/clk_out1 (100 MHz)</strong> を選択します。</td>
    </tr>
    <tr class="odd">
    <td><strong>proc_sys_reset_1<br />
    </strong>- slowest_sync_clk</td>
    <td>このリセットが同期されるクロック ソース。</td>
    <td>[Clock Source] オプションのドロップダウン リストから <strong>/clk_wiz_0/clk_out2 (142 MHz)</strong> を選択します。</td>
    </tr>
    <tr class="even">
    <td><strong>proc_sys_reset_2<br />
    </strong>- slowest_sync_clk</td>
    <td>このリセットが同期されるクロック ソース。</td>
    <td>[Clock Source] オプションのドロップダウン リストから <strong>/clk_wiz_0/clk_out3 (166 MHz)</strong> を選択します。</td>
    </tr>
    <tr class="odd">
    <td><strong>proc_sys_reset_3<br />
    </strong>- slowest_sync_clk</td>
    <td>このリセットが同期されるクロック ソース。</td>
    <td>[Clock Source] オプションのドロップダウン リストから <strong>/clk_wiz_0/clk_out4 (200 MHz)</strong> を選択します。</td>
    </tr>
    </tbody>
    </table>

4.  [OK] をクリックすると、設定が保存され、[Run Connection Automation] ダイアログ ボックスが閉じます。

#### デザインの残りの部分の手動接続

複数のデザイン依存ソースを持つ可能性のある接続を手動でして、デザインを終了します。このプラットフォーム例の場合、PS ブロックで生成されたリセット出力を使用して PL 側のロジックのリセット入力を制御します。特に、Processor System Reset IP ブロックおよび Clocking Wizard のクロック ソースをこの方法で接続します。また、PL Processor System Reset ブロックはクロック ソースがリクエストされた周波数にロックされるまでリセット ステートのままにします。さらに、PL で生成された割り込みは PS ブロックの割り込みリクエスト入力に接続します。

1.  ZYNQ7 Processing System IP の **FCLK_RESET0_N** 出力ピンを Clocking Wizard の **resetn** 入力ピンに接続します。

2.  ZYNQ7 Processing System IP の **FCLK_RESET0_N** 出力ピンを Processor System Reset IP ブロックの (proc_sys_reset_0、proc_sys_reset_1、proc_sys_reset_2、proc_sys_reset_3) の **ext_reset_in ** ピンに接続します。

3.  Clocking Wizard IP の **locked** 出力ピンを Processor System Reset IP ブロック (proc_sys_reset_0、proc_sys_reset_1、proc_sys_reset_2、proc_sys_reset_3) の **dcm_locked** 入力ピンに接続します。

4.  Concat ブロックの **dout[0:0]** 出力ピンを ZYNQ7 Processing System IP ブロックの **IRQ_F2P[0:0]** 入力ピンに接続します。

5.  [Regenerate Layout] ボタンをクリックし、ブロック デザインを描画し直します。

6.  この段階でブロック図は次のようになります。

    ![](./images/image31.png)

7.  [Validate] ボタンをクリックしてデザインが正しいかどうかを検証します。

    ![](./images/image32.png)

8.  [OK] をクリックして [Validate Design] ダイアログ ボックスを閉じます。

9.  ツールバーのフロッピー ディスク アイコンをクリックするか **Ctrl**+**S** キーを押して、ブロック デザインを保存します。

</details>

<details>
<summary><big><strong>手順 3: プラットフォーム ハードウェア インターフェイスの宣言</strong></big></summary>

IP インテグレーターのハードウェア プラットフォーム デザインを終了したら、SDSoC アクセラレータの接続ポイントとして使用可能なハードウェア インターフェイスおよびそれらと通信するデータ ムーバーを宣言する必要があります。これらの宣言は、プラットフォーム名を定義して、sds++ システム コンパイラで使用可能な特定のクロック、割り込み、およびバス インターフェイスを指定するプラットフォーム (PFM) プロパティを設定するとデザインに追加され、ハードウェア アクセラレーションで拡張されたシステムを作成するのに使用できます。これらのプロパティはプロジェクトに保存されます。

PFM プロパティはブロック デザイン (BD) 内に含まれるので、既存プラットフォームから開始して変更をすると、プロパティ セットがそのプラットフォーム用のものにはならず、ハードウェア プラットフォームが無効になることがあります。このような場合は、競合のある PFM プロパティの設定を解除してください。

この演習では、[Platform Interfaces] ウィンドウを使用してハードウェア インターフェイスを宣言します。  これらの PFM はブロック デザインに追加されます。  Vivado の [Block Properties] タブまたは [Tcl Console] ウィンドウを使用しても、ハードウェア インターフェイスを宣言する PFM プロパティを設定できます。  同等の結果になる Tcl コマンドは、このセクションの最後に示します。

このブロック デザインには、Clocking Wizard を使用して生成された異なるクロックが 4 つ含まれています。この演習では、このクロックの 1 つのみをイネーブルにし、それ以外は SDx IDE で使用できるようにします。SDx IDE を使用すると、ハードウェアでアクセラレーションする関数とハードウェア アクセラレータのクロック ソースを選択できます。同様に、ハードウェア関数とのデータ移動に使用可能な AXI ポートを宣言します。これらの AXI ポートはブロック デザインには直接表示されないこともありますが、SDx 環境で使用できるように PFM プロパティ設定には含まれます。

#### [Platform Interfaces] タブでのイネーブル

1.  Vivado のメイン メニューで [Window] → [Platform Interfaces] をクリックします。

2.  [Enable platform interfaces] リンクをクリックして [Platform Interfaces] タブを開きます。

    ![](./images/image33.png)

    [Platform Interfaces] タブには、ブロック デザイン内で使用可能なインターフェイスすべてが表示され、オン/オフにすることで、SDx 環境でハードウェア アクセラレータを作成する際に使用できるかどうかを指定できます。インターフェイスまたは選択したインターフェイスのグループを右クリックし、[Enable] をクリックすると、インターフェイス名の前の淡色表示のアイコンが色付きのアイコンに変わります。インターフェイスをイネーブルにすると、SDx ツールで使用できるようになります。

    ![](./images/image34.png)

#### プラットフォーム名
[Platform Interfaces] タブでイネーブルにすると、ブロック デザインのプラットフォーム名のプロパティが自動的に設定されます。プラットフォームのベンダー、ライブラリ、名前、およびバージョンは、[Source File Properties] ウィンドウのブロック デザインの [Properties] タブの PFM_NAME プロパティで変更できます。プロパティ テキスト ボックスの右側の鉛筆アイコンをクリックすると、プロパティを編集できます。このチュートリアルでは、既に設定済みのデフォルトをそのまま使用します。

![](./images/image35.png)

#### プラットフォーム クロック

Clocking Wizard で生成された 4 つの出力クロックは、次の図のように **clk_wiz_0** クロック (**clk_out1**、**clk_out2**、**clk_out3**、**clk_out4**) を [Platform Interfaces] タブでイネーブルにすると、sds++ システム コンパイラで使用できるように宣言されます。[Platform Interface Properties] ウィンドウでは、**is_default** プロパティがオンになっているので、**clk_out1** がデフォルトのアクセラレータ クロックとして選択されています。宣言されたクロックには、Processor System Reset IP ブロックを使用して同期済みリセット信号が関連付けられている必要があります。宣言されたクロックは、それぞれ [Platform Interface Properties] ダイアログ ボックスの **proc_sys_reset** プロパティで関連付けることができます。複数のプラットフォーム クロック周波数を指定すると、Vivado インプリメンテーション ツールが起動されたときに、配線されてタイミング制約を満たす可能性の高いアクセラレータ クロックを選択できるようになります。特定のクロック周波数を選択してタイミングが満たされなかった場合は、より周波数の小さなクロック ソースで問題が回避できることがあります。

   >**:pushpin: 注記:**
   >範囲を選択するには、ラインをクリックして、<kbd>Shift</kbd> キーを押しながら別のラインをクリックすると、その間にあるラインを選択できます。


1. **clk_wiz_0** クロックをそれぞれ右クリックして [Enable] をオンにします。

2. **clk_out1**、**clk_out2**、**clk_out3**、**clk_out4** をイネーブルにします。

4.  [Platform Interfaces] タブで [clk_out1] を選択します。

5.  clk_out1 の [Platform Interfaces Properties] の [Options] タブで [is_default] チェック ボックスをオンにします。

    ![](./images/image36.png)

#### プラットフォーム AXI ポート

このカスタム プラットフォームの場合、sds++ システム コンパイラで使用できるように、すべての Zynq-7000 SoC PS 側のマスターおよびスレーブ AXI ポートが宣言されています。FCLK_CLK0 インターフェイスは既に Clocking Wizard IP に 50.000 MHz 入力クロックを提供するためにハードウェアで使用されているので、SDx アクセラレータでの使用目的にはイネーブルにしません。

FCLK_CLK0 以外の各 **processing_system_7_0** インターフェイス (**M_AXI_GP0**、**M_AXI_GP1**、**S_AXI_ACP**、**S_AXI_HP0**、**S_AXI_HP1**、**S_AXI_HP2**、**S_AXI_HP3**) を右クリックし、[Enable] をクリックします。

![](./images/image37.png)

#### プラットフォーム割り込み

PL ロジックからの割り込みソースは **Concat** IP ブロックを介して Zynq-7000 SoC の割り込みリクエスト入力ポートに接続する必要があります。次の図は、sds++ システム コンパイラで使用できるように 16 個の割り込みソースがイネーブルになっています。

**xlconcat_0** 入力の **In0** から **In15** を右クリックし、[Enable] を選択します。


![](./images/image38.png)

[Platform Interfaces] タブから設定したプロパティは、プラットフォーム インターフェイス (processesing_system_7_0、clk_wiz_0、xlconcat_0) をクリックすると、[Block Properties] ダイアログ ボックスの PFM プロパティに表示されます。次の図は、xlconcat_0 の PFM プロパティを示しています。

![](./images/image39.png)

#### Tcl コンソール コマンド

次に示すコマンドは参照用であり、前のセクションで説明したように [Platform Interfaces] タブでイネーブル/ディスエーブルにしてプラットフォーム プロパティを設定した場合は必要ありません。Vivado ジャーナルまたはログ ファイルからは、[Platform Interfaces] タブで実行された動作で生成された Tcl コマンドの履歴を確認できます。

1.  PFM NAME
    ```
    set_property PFM_NAME “vendor:lib:zynq7_board:1.0”\  
    [get_files [get_property FILE_NAME [get_bd_designs]]]
    ```
2.  PFM CLOCK
    ```
        set_property PFM.CLOCK {\  
        clk_out1 {id “1” is_default “true”\  
        proc_sys_reset “proc_sys_reset_0”}\  
        clk_out2 {id “2” is_default “false”\ 
        proc_sys_reset “proc_sys_reset_1”}\  
        clk_out3 {id “3” is_default “false”\  
        proc_sys_reset “proc_sys_reset_2”}\  
        clk_out4 {id “4” is_default “false”\  
        proc_sys_reset “proc_sys_reset_3”}\  
        } [get_bd_cells /clk_wiz_0]
    ```
3.  PFM AXI ポート
    ```
        set_property PFM.AXI_PORT {\  
        M_AXI_GP0 {memport “M_AXI_GP” sptag “” memory “”}\  
        M_AXI_GP1 {memport “M_AXI_GP” sptag “” memory “”}\ 
        S_AXI_ACP {memport “S_AXI_ACP” sptag “” memory “”}\  
        S_AXI_HP0 {memport “S_AXI_HP” sptag “” memory “”}\  
        S_AXI_HP1 {memport “S_AXI_HP” sptag “” memory “”}\  
        S_AXI_HP2 {memport “S_AXI_HP” sptag “” memory “”}\  
        S_AXI_HP3 {memport “S_AXI_HP” sptag “” memory “”}\  
        } [get_bd_cells /processing_system7_0]
    ```
4.  PFM 割り込み
    ```
        set_property PFM.IRQ {\  
        In0 {} In1 {} In2 {} In3 {} In4 {} In5 {} In6 {} In7 {}\  
        In8 {} In9 {} In10 {} In11 {} In12 {} In13 {} In14 {} In15 {}\ 
        } [get_bd_cells /xlconcat_0]
    ```
</details>

<details>
<summary><big><strong>手順 4: HDL デザイン ファイルの生成</strong></big></summary>

デザインの HDL ファイルを生成します。

1. [Sources] ウィンドウで **zynq7_board.bd** ブロック デザインを右クリックし、[Generate Output Products] をクリックします。

    ![](./images/image40.png)

2. [Generate] をクリックします。

    ![](./images/image41.png)

3. [Design Runs] ウィンドウにチェックマークが表示されていれば、zynq7_board 出力が生成されています。

    ![](./images/image42.png)

4. IP インテグレーター ブロック デザインの最上位 HDL ラッパーを生成します。

5. [Sources] ウィンドウで **zynq7_board.bd** を右クリックし、[Create HDL Wrapper] をクリックします。

   ![](./images/image43.png)

6. [OK] をクリックします。

    ![](./images/image44.png)

7. Flow Navigator で [Generate Bitstream] をクリックします。


> これで SDSoC アクセラレーションのないハードウェア デザインを含む Zynq-7000 SoC ビットストリームが生成されました。このビットストリームは、ハードウェア アクセラレータを生成するために SDx IDE でデザインを実行する前に、ハードウェア機能をチェックするために使用できます。
</details>

<details>
<summary><big><strong>手順 5: DSA の出力</strong></big></summary>

ここまでで、IP インテグレーター ハードウェア デザインを PFM プロパティ デザイン メタデータと一緒にデバイス サポート アーカイブ (DSA) (SDSoC プラットフォームの定義の一部になった) に含めることができるようになりました。

1.  [Tcl Console] ウィンドウに次のように入力し、<kbd>Enter</kbd> キーを押します。

    `write_dsa -force -include_bit /<project dir>/<project name>/<project name>.dsa`

2.  [Tcl Console] ウィンドウに次のように入力し、DSA を確認します。

    `validate_dsa /<project dir>/<project name>/<project name>.dsa`

    ![](./images/image45.png)

3.  DSA を確認したら、Vivado GUI を閉じます。

</details>

### まとめ

演習 1 では、Vivado Design Suite の Vivado IP インテグレーターを使用して ZC702 ボードをターゲットにした Zynq-7000 SoC ハードウェア デザインを作成しました。ベース ハードウェア デザインを定義するため、PS Zynq7 Processor System IP のほか、クロック、リセット、および割り込み用に主な PL IP ブロックも追加しました。sds++ システム コンパイラで使用できるようにハードウェア インターフェイス選択を宣言した後、DSA を作成して SDSoC プラットフォームのハードウェア コンポーネントを定義しました。

<hr/>
<p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
