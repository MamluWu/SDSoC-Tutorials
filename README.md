<table style="width:100%">
  <tr>

<th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>SDSoC 環境チュートリアル</h2>
</th>

  </tr>
  <tr>
    <td width="17%" align="center">はじめに</a></td>
    <td width="16%" align="center"><a href="./getting-started-tutorial/README.md">SDSoC 環境チュートリアル: 概要</a></td>
    <td width="17%" align="center"><a href="./platform-creation-tutorial/README.md">SDSoC 環境チュートリアル: プラットフォームの作成 </a></td>
    <td width="17%" align="center"><a href="./opencv-to-xfopencv-migration-tutorial/README.md">SDSoC 環境チュートリアル: OpenCV の xfOpenCV への移行</a></td>	
  </tr>
</table>

## はじめに  

SDSoC™ (Software-Defined System-On-Chip) 環境は、Zynq®-7000 SoC および Zynq UltraScale+ MPSoC プラットフォームを使用してヘテロジニアス エンベデッド システムをインプリメントするための Eclipse ベースの統合設計環境 (IDE) です。SDSoC 環境では、ソフトウェア エンジニアおよびシステム アーキテクト用に、使いやすい Eclipse ベースの IDE を使用したエンベデッド C/C++ アプリケーション開発環境と、ヘテロジニアス Zynq SoC 開発用の包括的なデザイン ツールが提供されています。SDSoC 環境には、プログラマブル ロジックでの自動ソフトウェア アクセラレーションや、システム接続の自動生成などを実行する、フル システム最適化 C/C++/OpenCL コンパイラが含まれます。SDSoC 環境内のプログラミング モデルは、ソフトウェア エンジニアが簡単に理解できるように設計されています。アプリケーションは C/C++/OpenCL コードで記述され、プログラマがターゲット プラットフォームとハードウェアにコンパイルするアプリケーション内の関数のサブセットを特定します。この後、SDSoC システム コンパイラによりアプリケーションがハードウェアとソフトウェアにコンパイルされ、ファームウェア、オペレーティング システム、アプリケーション実行ファイルを含むブート イメージを含めた完全なエンベデッド システムが Zynq デバイスにインプリメントされます。

SDSoC 環境では、C/C++/OpenCL 関数の Zynq デバイス内の Arm CPU だけでなくプログラマブル ロジック ファブリックへのクロスコンパイルおよびリンクを含め、ソフトウェア抽象層を増加することによりハードウェアが抽象化されます。プログラマブル ハードウェアで実行するプログラム関数のユーザー仕様に基づいて、プログラム解析、タスク スケジューリング、プログラマブル ロジックおよびエンベデッド CPU へのバインディングが実行されるほか、ハードウェアおよびソフトウェア コード生成により、ハードウェアとソフトウェア コンポーネント間の通信および連携が調整されます。  

SDSoC 環境 2018.2 リリースでは、Zynq-7000 SoC を搭載した ZC702、ZC706、および Zed 開発ボード、Zynq UltraScale+ MPSoC を搭載した ZCU102 開発ボードがサポートされています。その他のプラットフォームは、パートナーから入手できます。詳細は、[SDSoC 環境のウェブ ページ](https://www.xilinx.com/products/design-tools/software-zone/sdsoc.md)を参照してください。  

## 関連情報
 - <a href="./getting-started-tutorial/README.md">SDSoC 環境チュートリアル: 概要</a>
 - <a href="./platform-creation-tutorial/README.md">SDSoC 環境チュートリアル: プラットフォームの作成 </a>
 - <a href="./opencv-to-xfopencv-migration-tutorial/README.md">SDSoC 環境チュートリアル: OpenCV の xfOpenCV への移行</a>
