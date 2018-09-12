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
    <td width="17%" align="center"><a href="Lab2-Creating-Software-Components.md">演習 2: プラットフォーム用ソフトウェア コンポーネントの作成</a></td>
    <td width="17%" align="center">演習 3: SDx IDE を使用したカスタム プラットフォームの作成</td>
  </tr>
</table>
</div>


## 演習 3: SDx IDE を使用したカスタム プラットフォームの作成

<details><summary><big><strong>手順 1: SDx&trade; IDE の起動とプラットフォーム プロジェクトの作成</strong></big></summary>

#### Linux ホスト マシン

シェル プロンプトに次のコマンドを入力します。
   
   1. `source <Xilinx_Install_Directory>/SDx/<Version>/settings64.{sh,csh}`
   2. `sdx`
    
1 つ目のコマンドで SDx IDE を起動する前に環境変数を設定し、2 つ目のコマンドで SDx IDE を起動しています。 

#### Windows ホスト マシン

Windows ホスト マシンの場合は、次のいずれかの方法で Vivado&reg; を起動します。

   - Vivado デスクトップ アイコンをクリックします。

   - [スタート] メニューから [Xilinx Design Tools] → [Vivado 2018.2] → [Vivado 2018.2] をクリックします。

   - コマンド プロンプト ウィンドウで次のコマンドを入力します。
   
      1. `<Xilinx_Install_Directory>/SDx/<Version>/settings64.bat`
      2. `sdx`
    
     1 つ目のコマンドで SDx IDE を起動する前に環境変数を設定し、2 つ目のコマンドで SDx IDE を起動しています。 
   
   SDx IDE ワークスペースのディレクトリを設定する画面になります。SDx ワークスペースには、プラットフォームと開発したアプリケーション プロジェクトが含まれます。
   
1.  この演習では、次の図に示すようにワークスペースに `/tmp/sdx_workspace` と入力します。

    ![](./images/image46.png)
    
    >:pushpin: **注記:**
    >演習 2 と同じ SDx ワークスペースを再利用します。

1.  [OK] をクリックします。

1. SDx IDE の [Welcome] 画面で [Create SDx Project] をクリックします。

   または、SDx IDE メニュー から [File] → [New\] → [SDx Project] をクリックします。

1.  [Project Type] ページで [Platform] をオンにします。

   ![](./images/image70.png)

    SDSoC&trade; プラットフォームを作成し、前の演習で作成したハードウェアおよびソフトウェア コンポーネントを使用して生成します。

2. [Next] をクリックします。

3.  [Platform Specification] ページで次を指定します。

    1. [Browse] をクリックして [Hardware specification file] に **/tmp/sdx\_workspace/zynq7\_board.dsa** を指定します。

    1. デフォルトのまま [Import software platform components] をオンにしておきます。

4. [Finish] をクリックします。

   ![](./images/image71.png)
</details>

<details><summary><big><strong>手順 2: システム コンフィギュレーションの指定</strong></big></summary>

zynq7\_board プロジェクトが作成されます。プロジェクトには [Project Explorer] ビューまたは [Assistant] ビューからアクセスできます。編集エリアには、zynq7\_board プラットフォームを生成するのに使用する 4 つの段階が示されます。

![](./images/image72.png)

1.  [(1) Define System Configuration] をクリックします。

    1.  [Name] テキスト ボックスに「standalone」と入力します。

    2.  [Display Name] には、同じ名前が自動的に入力されます。

    3.  [Description] テキスト ボックスに **standalone configuration for the zynq7\_board** と入力します。

    4.  [Boot Directory] で [Browse] ボタンをクリックして、**/tmp/sdx_workspace/boot** フォルダーを指定します。

    5.  [Bif File] で [Browse] ボタンをクリックして、**/tmp/sdx_workspace/boot/platform.bif** ファイル パスを指定します。

        ![](./images/image73.png)

    6.  [OK] をクリックします。

        ![](./images/image74.png)

</details>
<details>
<summary><big><strong>手順 3: プロセッサ ドメインの追加</strong></big></summary>

1.  [(2) Add Processor Group/Domain] をクリックします。

    1.  [Name] テキスト ボックスに **a9_standalone** と入力します。

    2.  [Display Name] には、同じ名前が自動的に入力されます。

    3.  \[OS] ドロップダウン リストから [standalone] を選択します。

    4.  [プロセッサ] ドロップダウン リストから **ps7_cortexa9_0** を選択します。

    5.  [Supported Runtime] ドロップダウン リストから **C/C++** を選択します。

    6.  [Linker Script] で [Browse] ボタンをクリックして、**/tmp/sdx_workspace/boot/lscript.ld** ファイル パスを指定します。

        ![](./images/image75.png)

    7.  [OK] をクリックします。

        ![](./images/image76.png)
</details>
<details>
<summary><big><strong>手順 4: プラットフォームの生成</strong></big></summary>

1.  [(3) Generate Platform] をクリックします。
    次のメッセージが表示されます。
    ![](./images/image77.png)

2.  [OK] をクリックします。
    ![](./images/image78.png)
</details>

<details>
<summary><big><strong>手順 5: カスタム プラットフォームの IP リポジトリへの追加</strong></big></summary>


1.  [(4) Add to Custom Repositories] をクリックします。

    ここでは、新しく作成したプラットフォームを SDSoC アプリケーションのビルドに使用可能なプラットフォームの選択リストへ追加します。

      ![](./images/image79.png)

2.  [Project Explorer] および [Assistant] ビューには、新しい zynq7_board プラットフォームが表示されます。

    ![](./images/image80.png)

</details>
<details>
<summary><big><strong>手順 6: カスタム プラットフォームをターゲットとする SDx アプリケーションの作成</strong></big></summary>

1.  SDx IDE メニュー 、から [File] → [New] → [SDx Project] をクリックして新しいアプリケーション プロジェクトの作成を開始します。

1.  [Project Type] ページで [Application] をオンにします。

    生成したカスタム SDSoC プラットフォームに対して SDx アプリケーションを作成します。

    ![](./images/image81.png)

1.  [Next] をクリックします。

1.  [Create a New SDx Project] ダイアログ ボックスでプロジェクトの名前に **sdx\_app1** と入力します。

1.  [Next] をクリックします。

    ![](./images/image82.png)

1.  [Platform] ページで [Platform] をオンにします。

1.  **zynq7_board [custom]** プラットフォーム名をクリックします。

    ![](./images/image83.png)

1.  \[System configuration] ページは、デフォルト設定のままにします。

    - \[System configuration]: **standalone**
    - \[Runtime]: **C/C++**
    - \[Domain]: **a9_standalone**
    - \[CPU]: ps7_cortexa9_0
    - \[OS]: standalone
    - \[Output type]: **Executable (elf)**
    - \[Allow hardware acceleration] をオン

1. [Next] をクリックします。

   ![](./images/image84.png)

1.  [Templates] ページで [SDx Examples] ボタンをクリックして使用可能なテンプレートをアップデートします。

    ![](./images/image85.png)

1.  [SDx Examples] ページでは、使用可能な例を確認できます。ダウンロードされたテンプレートは、新規プロジェクトを作成する際にテンプレートとして使用できるようになります。

    ![](./images/image86.png)

1.  [Download] をクリックしてリポジトリから [SDSoC Examples] をダウンロードします。

    ![](./images/image87.png)

1.  [OK] をクリックします。

    ![](./images/image88.png)

1. [Array Partitioning] を選択して [Finish] をクリックします。

    ![](./images/image89.png)

   新しく作成した SDSoC アプリケーション `sdx_app1` が [Project Explorer] ビューおよび [Assistant] ビューに表示されます。

   >:pushpin: **注記:**
   >[Assistant] ビューには、[Array Partitioning] 例の一部である `matmul_partition_accel` という名前のハードウェア アクセラレーション関数が表示されます。

    ![](./images/image90.png)

1. [Application Project Settings] ウィンドウには `sdx_app1` の設定が表示されます。

    ![](./images/image91.png)

1. [Assistant] ビューで **sdx_app1 [SDSoC]** を展開します

1. [Debug[Hardware]] を右クリックして [Build] をクリックします。

   ![](./images/image92.png)

1. [Assistant] ビューには、次のリンクを使用したビルド結果が表示されます。

   - コンパイル ログ

   - アクセラレータのデータ モーション レポート

   - 生成された SD カード イメージの内容

   ![](./images/image93.png)

1. [Console] ビューをスクロールすると、**sds.log** に含まれる個別のビルド段階を確認することもできます。

    ![](./images/image94.png)

1. [Assistant] ビューで [SD Card Image] を右クリックし、[Open] → [Open in File Browser] をクリックして、ディスクの SD カードの内容を表示します。`sd_card` ディレクトリは `/tmp/sdx_workspace/sdx_app1/Debug/sd_card` の SDx ワークスペース内に含まれます。

1. SD カードの内容を FAT32 フォーマットの SD カードにコピーして、この SD カードを使用して ZC702 ボードを起動して、ターミナル プログラムで sdx\_app1 UART 出力を確認します。

1. 演習 2 の hello\_world と同じボード設定およびブート手順を使用できます。

   ![](./images/image97.png)
</details>

### まとめ

演習 3 では、スタンドアロン ソフトウェア ランタイム環境を使用して ZC702 をターゲットにしたカスタム SDSoC プラットフォームを作成しました。また、カスタム SDSoC プラットフォーム (zynq7\_board) 上に SDSoC の配列パーティションの例をビルドしました。SD カードからボードをブートしてアプリケーションの出力メッセージを確認することで、ZC702 ボードを使用してハードウェア アクセラレータの配列パーティションを検証しました。


<hr/>
<p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
