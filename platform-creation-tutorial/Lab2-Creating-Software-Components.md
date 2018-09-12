<div style="page-break-after: always;"></div>
<div style="display: none;" media="print">
<table style="width:100%">
  <tr>

<th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>演習: SDSoC プラットフォームの作成</h2>
</th>

  </tr>
  <tr>
    <td width="17%" align="center"><a href="README.md">概要</a></td>
    <td width="16%" align="center"><a href="Lab1-Creating-DSA-for-Zynq-7000-SoC-Processor-Design.md">演習1: Zynq-7000 SoC プロセッサ デザイン用の DSA の作成</a></td>
    <td width="17%" align="center">演習 2: プラットフォーム用ソフトウェア コンポーネントの作成</td>
    <td width="17%" align="center"><a href="Lab3-Creating-Custom-Platform-Using-the-SDx-IDE.md">演習 3: SDx IDE を使用したカスタム プラットフォームの作成</a></td>
  </tr>
</table>
</div>


## 演習 2: プラットフォーム用ソフトウェア コンポーネントの作成  
この演習では、スタンドアロン ターゲット用に SDSoC&trade; プラッフォーム ターゲットのソフトウェア コンポーネントを定義するのに使用するファイルを作成します。作成するソフトウェア コンポーネントは、次のとおりです。

- **FSBL (第 1 段階ブートローダー)**: FSBL は PS ブロックを初期化し、必要であればビットストリームを使用して PL をコンフィギュレーションし、第 2 段階ブートローダーまたはブート ファイルで指定したエンド アプリケーションのいずれかを読み込みます。

- **リンカー スクリプト**: リンカー スクリプトは実行可能なソフトウェア アプリケーション ファイル内のコードおよびデータ領域に使用するメモリ アドレス空間を指定します。実行可能ファイルは、ELF (Executable and Linkable Format) にフォーマットされます。スタックおよびヒープ サイズもリンカー スクリプト内で指定します。

- **ブート イメージ フォーマット (BIF) ファイル**: BIF ファイルは、Zynq&reg;-7000 SoC ブート ファイル (BOOT.BIN) の構築方法を指定します。ブート ファイルには、FSBL と実行可能アプリケーション ELF ファイルが含まれます。また、ビットストリームだけでなく、追加プログラムおよびデータ ファイルも含まれます。

>:pushpin: **注記:**
>ブート イメージの作成に関する詳細は、『Zynq-7000 SoC ソフトウェア開発者向けガイド』 ([UG821](https://japan.xilinx.com/support/documentation/user_guides/j_ug821-zynq-7000-swdev.pdf)) を参照してください。

<details>
<summary><big><strong>手順 1: SDx&trade; IDE の起動とアプリケーション プロジェクトの作成</strong></big></summary>


#### Linux ホスト マシン

シェル プロンプトに次のコマンドを入力します。
   
   1. `source <Xilinx_Install_Directory>/SDx/<Version>/settings64.{sh,csh}`
   2. `sdx`
    
1 つ目のコマンドで SDx を起動する前に環境変数を設定し、2 つ目のコマンドで SDx IDE を起動しています。 

#### Windows ホスト マシン

Windows ホスト マシンの場合は、次のいずれかの方法で Vivado&reg; を起動します。

   - Vivado デスクトップ アイコンをクリックします。

   - [スタート] メニューから [Xilinx Design Tools] → [Vivado 2018.2] → [Vivado 2018.2] をクリックします。

   - コマンド プロンプト ウィンドウで次のコマンドを入力します。
   
      1. `<Xilinx_Install_Directory>/SDx/<Version>/settings64.bat`
      2. `sdx`
    
     1 つ目のコマンドで SDx を起動する前に環境変数を設定し、2 つ目のコマンドで SDx IDE を起動しています。 

SDx IDE が開いたら、SDx ワークスペースのディレクトリを設定する画面になります。SDx ワークスペースには、これから開発するプラットフォームとアプリケーション プロジェクトが含まれるようになります。

1. この演習では、次の図のように「**/tmp/sdx\_workspace**」と指定します。

   ![](./images/image46.png)

1. [OK] をクリックします。

1. SDx IDE の [Welcome] 画面で [Create SDx Project] をクリックします。

   または、SDx IDE メニュー から [File] → [New] → [SDx Project] をクリックします。

   ![](./images/image47.png)

1. [Project Type] ページで [Application] をオンにします。

   ![](./images/image48.png)

   まず、SDSoC プラットフォームに必要なソフトウェア アプリケーション コンポーネントを作成してから、演習後半でこのソフトウェアで使用するプラットフォーム プロジェクトを作成します。

1. [Next] をクリックします。

</details>

<details>
<summary><big><strong>手順 2: FSBL アプリケーションの作成</strong></big></summary>


1. [Create a New SDx Project] ページでプロジェクトの名前に **fsbl** と入力します。

    ![](./images/image49.png)

1. [Next] をクリックします。

1. [Platform] ページで [Hardware specification (DSA/HDF)] をオンにします。

    ![](./images/image50.png)

1. [Add New DSA/HDF] をクリックします。

1. [Add New DSA/HDF] ダイアログ ボックスで演習 1 でエクスポートした HDF ファイルを指定します。

    ![](./images/image51.png)

1. **/tmp/zynq7\_board/zynq7\_board.sdk/zynq7\_board\_wrapper.hdf** を選択します。

1. [OK] をクリックします。

   新しいプラットフォーム名がダイアログ ボックスに追加されます。

1. タイプが HDF の **zynq7\_board\_wrapper** を選択します。

1. [Next] をクリックしてこのハードウェア仕様ファイルを読み込みます。

   ![](./images/image52.png)

1. [System configuration] ページはデフォルトの設定のままにして [Next] をクリックします。

   - \[OS Platform]: **standalone**

   - \[Processor]: **ps7\_cortexa9\_0**

   - \[Language]: **C**

   ![](./images/image53.png)

1. [Templates] ページで [Zynq FSBL] を選択し、[Finish] をクリックします。

    ![](./images/image54.png)

   次の図に示すように、Zynq-7000 SoC FSBL アプリケーションが作成されます。

    ![](./images/image55.png)

8. [Assistant] ビューで **fsbl [Application]** を展開します。[Assistant] ビューは SDx IDE の [Project Explorer] ビューの下に表示されます。

   ![](./images/image56.png)

<a name="buildfsbl"></a>
  
9. [Debug] を右クリックして [Build] をクリックします。

   SDx の [Console] ビューに FSBL コンパイル段階と、作成された **fsbl.elf** 実行可能ソフトウェア ファイルの容量が表示されます。

    ![](./images/image57.png)
</details>
<details>
    <summary><big><strong>手順 3: アップデートしたリンカースクリプトを使用して Hello World ソフトウェア アプリケーションを作成</strong></big></summary>


Hello World アプリケーションを基本的なハードウェアおよびソフトウェアの機能の基盤として使用し、SDSoC プラットフォームおよびハードウェア アクセラレーションされた関数を作成する前にデザインとボードの動作を確認します。Zynq ファミリのブート プロセスには、アプリケーション ソフトウェア (この場合は hello\_world コード) か U-Boot などの第 2 段階ブートローダーのいずれかを読み込む第 1 段階ブートローダー (FSBL) が含まれます。次に FSBL がビットストリームを使用して PL をコンフィギュレーションし、アプリケーションのリンカー スクリプトで定義したとおりにアプリケーション コードをメモリに読み込みます。これで、プログラム制御がアプリケーション コードに渡されます。

1. SDx IDE メニュー バーで [File] → [New] → [SDx Project] をクリックして、新しいアプリケーションを作成します。

2. [Project Type] ページで [Application] をクリックします。

3. [Create a New SDx Project] ダイアログ ボックスの [Project name] フィールドに「hello_world」と入力して、[Next] をクリックします。

    ![](./images/image58.png)

4. [Platform] ページで [Platform] をオンにして `zynq7_board_wrapper_1[custom]` プラットフォームを選択します。


   ![](./images/image59.png)

5. [Next] をクリックします。

6. \[System configuration] ページは次のようにデフォルトの設定のままにして [Next] をクリックします。

   - \[System configuration]: **systemconfig**

   - \[Runtime]: **C/C++**

   - \[Domain]: **standalone\_domain**

   - \[CPU]: **ps7_cortexa9_0**

   - \[OS]: **standalone**

   - \[Output type]: **Executable (elf)**

   - \[Language]: **C**

   ![](./images/image60.png)

7. [Templates] ページで [Hello World] アプリケーション テンプレートを選択します。

   ![](./images/image61.png)

8. [Finish] をクリックします。
   SDx アプリケーション プロジェクトが作成されます。

9. [Assistant] ビューで `hello_world [Application]` を右クリックして、[Generate Linker Script] をクリックします。

    ![](./images/image62.png)

8. [Generate linker script] ページの右側の [Basic] タブで [Heap Size] と [Stack Size] を変更します。デフォルト値のままでは入力および出力データ バッファーのヒープを使用する典型的な SDx アプリケーションには小さすぎます。

   >:pushpin: **注記:**
   >[Generate linker script] ページの値には、コンマおよびその他のフォーマットが使用できません。

   1. [Heap Size] を **805306368** に設定します。
      これは 768 MB です。

   1. [Stack Size] を **262144** に設定します。
      これは 256 KB です。

    ![](./images/image63.png)

9. [Generate] をクリックします。

10. [Yes] をクリックして既存のリンカー スクリプトを上書きします。

9.  [Assistant] ビューで `hello_world [Application]` を展開します。

    ![](./images/image64.png)

10. [Debug] を右クリックして [Build] をクリックします。

SDx の [Console] ウィンドウに hello\_world コンパイル段階と、作成された **hello\_world.elf** 実行可能ソフトウェア ファイルの容量が表示されます。

![](./images/image65.png)
</details>

  <details>
    <summary><big><strong>手順 4: ブート イメージの生成</strong></big></summary>

ここまでで、Zynq-7000 SoC を起動するのに使用できるハードウェアおよびソフトウェア コンポーネントのセットができました。SDx IDE には、ブート イメージおよびそのブート イメージ コンポーネントを定義するブート イメージ ファイル (BIF) を作成する機能があります。BIF にリストされるパーティションの順序は重要です。**hello_world/_sdx/bootimage** ディレクトリに生成された **BOOT.bin** ファイルは FAT32 フォーマットの SD カードに含めて、ZC702 を起動するのに使用できます。UART に Hello World というテキスト文字列が出力され、ビットストリームで PL がコンフィギュレーションされます。

1.  [Assistant] ビューで `hello_world [Application]` を右クリックして、[Create Boot Image] をクリックします。

    ![](./images/image66.png)

2.  [Create Boot Image] ダイアログ ボックスはデフォルトのままにします。

    >:pushpin: **注記:**
    > [Create Image] ボタンをクリックする前に、ブート イメージのパーティションが次の順番になるようにしてください。順番を次の図と同じにするには、ビットストリーム パーティションを選択して、[Up] ボタンをクリックして順番を調整する必要があることもあります。

    1. [Zynq] アーキテクチャを選択します。

    2. [Create new BIF file] をオンにします。

    3. [Output BIF file path] を指定します。たとえば、``/tmp/sdx_workspace/hello_world/_sdx/bootimage/hello_world.bif`` のように指定します。

    4. [Output path] を指定します。たとえば、``/tmp/sdx_workspace/hello_world/_sdx/bootimage/BOOT.bin`` のように指定します。

    5. ブート イメージ パーティションを次の順番で追加します。

       1. `/tmp/sdx_workspace/zynq7_board_wrapper_1/export/zynq7_board_wrapper_1/sw/systemconfig/boot/fsbl.elf`

       2. `/tmp/sdx_workspace/_sdx/bitstream/zynq7_board_wrapper.bit`

       3. `/tmp/sdx_workspace/hello_world/Debug/hello_world.elf`

      ![](./images/image67.png)

      >:pushpin: **注記:**
      >上記のブート イメージ パーティションのリストに fsbl.elf がデフォルトで含まれない場合は、[手順 2](#buildfsbl)  (/tmp/sdx_workspace/fsbl/Debug/fsbl.elf) でビルドした FSBL をパーティション リストに追加して使用できます。パーティションを変更するには、まずそれを選択して [Delete] をクリックします。[Add] をクリックして、パーティション リストに追加する新しいファイルを指定します。順序は [Up]/[Down] ボタンで並べ替えます。
      
    6. [Create Image] をクリックします。
    
       ブート イメージおよびそのブート イメージ コンポーネントを定義するブート イメージ ファイル (BIF) が作成されます。

</details>
<details>
<summary><big><strong>手順 5: 生成したブート イメージ (BOOT.bin) を使用して ZC702 を起動</strong></big></summary>


1. `/tmp/sdx_workspace/hello_world/_sdx/bootimage/BOOT.bin` の `BOOT.bin` ファイルをホスト マシンではなく、FAT32 フォーマットの SD カードにコピーします。

2. SD カードを安全に取り出すか、アンマウントします。

3. ZC702 (SW11) ボードの電源をオフにします。

4. ルート ディレクトリに `BOOT.bin` を含む SD カードを、ZC702 の金属の SD カード ホルダー (J64) に挿入します。

5. ZC702 ブート モードの DIP スイッチ (SW16) は SD カード ブート用に設定します。次は、推奨される DIP スイッチ位置設定のリストです。

   - 1 - スイッチに印刷された番号 1 から離れた方

   - 2 - スイッチに印刷された番号 2 から離れた方

   - 3 - スイッチに印刷された番号 3 に向かう方

   - 4 - スイッチに印刷された番号 4 に向かう方

   - 5 - スイッチに印刷された番号 5 から離れた方

6.  mini-USB ケーブルを ZC702 USB-UART コネクタ (J17) にプラグします。

7.  反対側の USB Host Type A コネクタをホスト マシンの USB ポートにプラグします。

8.  ZC702 (SW11) ボードの電源を入れます。

9.  ZC702 の Silicon Labs CP210x USB-to-UART デバイスに接続された COM ポートを使用して、ホスト マシンで TeraTerm などのターミナル プログラムを実行します。

    1. Silicon Labs CP210x VCP ドライバーは既にホスト マシンにロード済みであることを確認します。

    2. Silicon Labs CP210x USB to UART Bridge を含む COM ポートに接続します。

       ZC702 では COM ポートをアクティベートするのにボード電源が必要です。

    3. シリアル ポートの設定は、115200、8-N-1、ハードウェア フロー制御なしにします。

10.  ターミナル プログラムが COM ポートに接続されたら、次のいずれかの方法でリブートします。

     - POR\_B pushbutton (SW1) を押す

     - ZC702 をパワー サイクル (SW11)

11. ZC702 がビットストリームで問題なくコンフィギュレーションされたら、DONE LED (DS3) が緑になります。

    これには、通常最大で 30 秒ほどかかります。

12. 問題なくブートされたら、ターミナル ウィンドウに Hello World というテキストが表示されます。

    ![](./images/image68.png)

</details>
    <details>
    <summary><big><strong>手順 6: プラットフォーム作成のためのディレクトリを準備</strong></big></summary>


ここまでで、ZC702 をターゲットにするスタンドアロン SDSoC プラットフォームを作成するのに必要なコンポーネントがすべて揃いました。SDx IDE のプラットフォーム作成のダイアログ ボックスで指定する入力ファイルを準備するためのディレクトリを作っておきます。ディレクトリの名前および位置はユーザーが選択できます。SDx ワークスペース ディレクトリに準備ディレクトリを作成します。次は、準備ディレクトリの生成する手順と、詳細な動作を終了するための Linux コマンドを示しています。

1.  ディレクトリを SDx ワークスペースのディレクトリに変更します。

2.  演習 1 で作成した DSA を SDx ワークスペースにコピーします。

3.  SDx ワークスペースにブート ディレクトリを作成します。

4.  ディレクトリをブートに変更します。

5.  FSBL ELF ファイルをブート ディレクトリにコピーします。

6.  `hello_world` リンカー スクリプトをブート ディレクトリにコピーします。

7.  `hello_world` BIF ファイルをブート ディレクトリにコピーします。

8.  `hello_world` BOOT.bin ファイルをブート ディレクトリにコピーします。
    ```
    cd /tmp/sdx_workspace
    cp /tmp/zynq7_board/zynq7_board.dsa .
    mkdir boot
    cd boot
    cp /tmp/sdx_workspace/fsbl/Debug/fsbl.elf .
    cp /tmp/sdx_workspace/hello_world/src/lscript.ld .
    cp /tmp/sdx_workspace/hello_world/_sdx/bootimage/hello_world.bif .
    cp /tmp/sdx_workspace/hello_world/_sdx/bootimage/BOOT.bin .
    ```
9.  `hello_world.bif` ファイルを **platform.bif** という名前のファイルにコピーします。

10. **platform.bif** ファイルを編集します。

    ![](./images/image69.png)

SDSoC ブート イメージ フォーマット ファイルは標準 BIF ファイルと類似していますが、ブート ファイルへのパスには直接パスではなく、かっこ (< >) で指定されたトークンが使用されます。BIF トークンは、SDSoC コンパイル時に実際のファイル名と生成された内容に置き換わります。これは、PL 領域のビットストリーム ファイルが SDx ツールで実行されると手続き型で生成され、BIF ファイルにリストされるエレメントのファイル名の中に BIF ファイルの生成時にはわかっていないものがあるためです。
</details>

### まとめ

演習 2 では、演習 1 の Vivado Design Suite でエクスポートしたハードウェア デザインを使用して、SDx 環境に読み込みました。SDx IDE を使用して FSBL ソフトウェア アプリケーションと hello\_world ソフトウェア アプリケーションを作成しました。hello\_world アプリケーションのリンカー スクリプトを SDx 環境に必要なリソースを反映させるためにアップデートしました。この後、これらのアプリケーションを使用してブート イメージ (BOOT.bin) を作成しました。ブート イメージを SD カードに書き込んで、ZC702 ボードを起動するのに使用して、ハードウェア上でアプリケーションを検証しました。準備ディレクトリを作成して、演習 3 用に関連ファイルを移動しました。

<hr/>
<p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
