# doc_genai_About_the_local_LLM_operating_environment
ローカルLLMの動作環境について


<br/><br/>
## 目次

- [ローカルLLMの動作環境](#ローカルllmの動作環境)
- [1. ローカルLLM動作環境要素比較表](#1-ローカルllm動作環境要素比較表)
- [2. 要素解説](#2-要素解説)
    - [2.1. 演算装置](#21-演算装置)
    - [2.2. メモリ](#22-メモリ)
    - [2.3. ストレージ](#23-ストレージ)
    - [2.4. API](#24-API)
    - [2.5. アプリケーション](#25-アプリケーション)
    - [2.6. モデル形式](#26-モデル形式)
    - [2.7. モデルサイズ](#27-モデル形式)
- [3. 図表による解説](#3-図表による解説)
    - [3.1. 構成図](#31-構成図)
    - [3.2. 関係図](#32-関係図)
    - [3.3. フロー図](#33-フロー図)
- [4. まとめ](#4-まとめ)


<br/><br/>
## ローカルLLMの動作環境

近年注目を集める大規模言語モデル (LLM) は、ローカル環境でも動作させることが可能です。
最適なハードウェアとソフトウェアを選択することが、ローカルLLMの性能を最大限に引き出す鍵となります。

この資料では、ローカルLLM動作環境の主要な要素について、表形式と説明文で解説し、理解を深めるための構成図、関係図、フロー図の提案を行います。


<br/><br/>
### 1. ローカルLLM動作環境要素比較表

| 区分 | 項目 | 種類 | 特徴 | パフォーマンスへの影響 | 備考 |
|---|---|---|---|---|---|
| ハードウェア | **演算装置** | CPU | 汎用性が高い、入手しやすい | 低～中 | 消費電力低 |
| | | GPU | 並列処理に優れる | 高 | 高速処理 |
| | | NPU | AI処理に特化 | 非常に高 | 最新モデルに対応 \| 対応機種が限られる |
| | **メモリ** | RAM | システムメモリ、高速アクセス | 中 | 容量不足でも拡張しやすい |
| | | VRAM | GPU搭載メモリ、高速アクセス | 高（GPU使用時） | 容量不足に注意 |
| | | 共有メモリ | CPU/GPU共有 | 高（適切な設計時） | 省電力。容量不足に注意。対応機種が限られる |
| | **ストレージ** | HDD | 大容量、低価格 | 低 | 読み込み速度が処理速度に影響 |
| | | SSD | 高速 | 中～高 | 読み込み速度が処理速度向上に貢献 \| 高価格 |
| ソフトウェア | **API** | PyTorch | 汎用性が高い | 低 | 設定が複雑 |
| | | CUDA | NVIDIA GPU向け | 高（NVIDIA GPU使用時） | 高速処理 \| NVIDIA製GPU必須 \| 設定が複雑 |
| | | DirectML | Windows向け | 中～高（Windows環境） | 高速処理 \| GPU必須 \| 設定が複雑 |
| | **アプリケーション** | Transformers | 柔軟性が高い | 中 | 使いやすい \| Python向け |
| | | llama.cpp | 最適化された実装 | 高 | 高速処理 \| C++製 |
| | | MLX | Mac Apple silicon 専用 | 高 | 高速処理 \| C++製 |
| | | ONNX Runtime | 相互運用性が高い | 中～高 | 高速処理 \| 設定が複雑 \| Python, C++, C# |
| | **モデル形式** | bin | 汎用性が高い | 中 | 推論速度が速い \| シンプルな構造 |
| | | gguf | 圧縮形式 | 高 | 推論速度と圧縮率のバランス \| llama.cpp 向け |
| | | safetensors | 圧縮形式 | 高 | 安全で効率的 \| PyTorch 向け |
| | | onnx | 相互運用性が高い | 中～高 | 汎用性が高い \| モデル変換が必要 |
| | **モデルサイズ** | 1-3B | 小型デバイス向け | 低 | 低スペックCPU \| スマートスピーカー、ウェアラブルデバイス |
| | | 3-9B | 研究、特定タスク向け | 低～中 | 中スペックCPU、GPU \| 自然言語処理、画像認識 (特定領域) |
| | | 9-27B | 多様なタスク向け | 中～高 | 高スペックCPU、GPU \| チャットボット、文章生成 |
| | | 27B以上 | 大規模な生成モデル | 高 | 高性能GPU、TPUクラスタ \| 大規模言語モデル、画像生成 |


<br/><br/>
### 2. 要素解説

#### 2.1. 演算装置

- **CPU**: 汎用性が高く、一般的なパソコンに搭載されているため入手しやすいですが、LLMに必要な並列処理能力は低いため、比較的処理速度は低くなります。
- **GPU**: グラフィックボードに搭載され、CPUよりも高速な並列処理が可能です。 
- **NPU**: ニューラルネットワーク処理に特化したチップで、GPUよりも高速かつ省電力です。最新モデルへの対応も期待できます。対応機種が限られます。


```mermaid
graph LR
    A[CPU] -->|処理速度| B[低〜中]
    C[GPU] -->|処理速度| D[高]
    E[NPU] -->|処理速度| F[非常に高]
```

<br/><br/>
#### 2.2. メモリ

- **RAM**: システムメモリであり、データの読み書き速度が速いです。モデルと中間結果を格納します。
- **VRAM**: GPUに搭載されたメモリで、RAMよりも高速な読み書き速度が可能です。GPU使用時にモデルと中間結果を格納します。拡張性が低いので、導入時に容量不足だと利用したいモデルのサイズによっては実行不可となります。
- **共有メモリ**: CPUとGPUとNPUが共有できるメモリで、両方の利点を活かせます。拡張性が低いので、導入時に容量不足だと利用したいモデルのサイズによっては実行不可となります。対応機種が限られます。


```mermaid
graph LR
    A[CPU] -->|利用| B[RAM]
    C[GPU] -->|利用| D[VRAM]
    E[CPU] -->|利用| F[共有メモリ]
    G[GPU] -->|利用| F
    H[NPU] -->|利用| F
```

<br/><br/>
#### 2.3. ストレージ

- **HDD**: 大容量ですが、データの読み書き速度が遅いため、処理速度に影響を与える可能性があります。モデルの保存などに用いられます。
- **SSD**: HDDよりも高速なデータ読み書きが可能です。モデルの保存に用いることで処理速度の向上に貢献します。


```mermaid
graph LR
    A[HDD] -->|読み書き速度| B[低速]
    C[SSD] -->|読み書き速度| D[高速]
```

<br/><br/>
#### 2.4. API

- **PyTorch**: モデルを直接実行します。
- **CUDA**: NVIDIA GPU向けに最適化されており、高速処理が可能です。
- **DirectML**: Windows環境向けに最適化されており、高速処理が可能です。


```mermaid
graph LR
    A[CUDA] -->|対応GPU| B[NVIDIA]
    C[DirectML] -->|対応OS| D[Windows]
```

<br/><br/>
#### 2.5. アプリケーション

- **Transformers**: Hugging Faceが提供するライブラリで、 Python向けに設計されており、使いやすいインターフェースを備えています。
- **llama.cpp**: C++で実装されたMacで実行可能な大規模言語モデルを簡便に構築・展開するためのフレームワークで、 Mac以外にもWindowsやLinuxで動作します。
- **MLX**: C++で実装されたMac Apple siliconでローカルに動作する大規模言語モデル(LLM)アプリケーション用のフレームワークで、 高速処理に特化しています。
- **ONNX Runtime**: ONNXモデル専用の実行環境です。 高速処理と汎用性を兼ね備えています。

```mermaid
graph TD
    B[Transformers]
    C[llama.cpp]
    
    B --> B1[Hugging Face提供]
    B --> B2[Python向け]
    B --> B3[使いやすいインターフェース]
    
    C --> C1[C++実装]
    C --> C2[簡易に構築]
    C --> C3[Mac, Windows, Linux で動作]
    
```
```mermaid
graph TD
    E[MLX]
    D[ONNX Runtime]
    
    E --> E1[C++実装]
    E --> E2[高速処理]
    E --> E3[Mac Apple silicon 専用]

    D --> D1[ONNXモデル専用]
    D --> D2[高速処理]
    D --> D3[汎用性]
```

<br/><br/>
#### 2.6. モデル形式

- **bin**: バイナリ形式。学習された直後の状態で配布される。他のモデル形式へ変換する前の状態。汎用性が高い特徴があります。
- **gguf**: llama.cpp 向けに開発された圧縮形式で、推論速度と圧縮率のバランスが取れています。
- **safetensors**: PyTorch用の安全で効率的なモデル保存形式。MLXでも利用可能。
- **onnx**: オープンフォーマットであり、 汎用性が高く様々なフレームワークに対応しているため、環境間でのモデルの移植が容易です。

```mermaid
graph LR
    A[bin] -->|変換| B[gguf]
    A -->|変換| D[safetensors]
    A -->|変換| C[onnx]
```

<br/><br/>
#### 2.7. モデルサイズ

#### モデルサイズと特徴

* **1-3B:** 小型デバイスに適した軽量モデル。低スペックなCPUで動作し、スマートスピーカーやウェアラブルデバイスなど、リソース制限のある環境での利用に適しています。
* **3-9B:** 研究用途や特定のタスクに特化したモデル。自然言語処理や画像認識といった特定の分野において、より高度な処理を可能にします。低～中程度のスペックのCPUやGPUが必要となります。
* **9-27B:** 多様なタスクに対応できる汎用的なモデル。チャットボットや文章生成など、幅広い用途に活用できます。中～高性能のCPUやGPUが必要となります。
* **27B以上:** 大規模な生成モデル。大規模言語モデルや高品質な画像生成など、高度なタスクを実行できます。高性能なGPUやTPUクラスタなどの大規模な計算資源が必要です。

#### モデルサイズと必要なハードウェア・環境

* モデルサイズが大きくなるにつれて、より高度なタスクに対応できるようになりますが、同時により高性能なハードウェアが必要となります。
* 小型モデルは低スペックなCPUで動作しますが、大規模モデルは高性能なGPUやTPUクラスタといった大規模な計算資源を必要とします。

#### モデルサイズとユースケース

* **小型モデル:** スマートスピーカー、ウェアラブルデバイスなど、リソース制限のある環境での利用
* **中型モデル:** 自然言語処理、画像認識 (特定領域) などの特定のタスク
* **大型モデル:** チャットボット、文章生成、大規模言語モデル、画像生成など、幅広い用途

**補足:**

* **B:** Billion（10億）の略で、モデルのパラメータ数を表す単位です。パラメータ数が多いほど、モデルは複雑な処理を実行できます。
* **CPU:** Central Processing Unit（中央処理装置）の略で、コンピュータの中心的な処理を行う部品です。
* **GPU:** Graphics Processing Unit（画像処理装置）の略で、特に並列処理に優れ、AI計算によく利用されます。
* **TPU:** Tensor Processing Unit（テンソル処理ユニット）の略で、Googleが開発したAI専用のハードウェアアクセラレータです。


```mermaid
graph TD
    subgraph モデルサイズ
        A((1-3B))
        B((3-9B))
        C((9-27B))
        D((27B+))
    end

    subgraph 実用性
        E{小型デバイス}
        F{研究、特定タスク}
        G{多様なタスク}
        H{大規模生成}
    end

    subgraph ハードウェア
        I(低スペックCPU)
        J(中スペックCPU/GPU)
        K(高性能GPU/TPU)
    end

    A --> E --> I
    B --> F --> J
    C --> G --> J
    D --> H --> K
```



<br/><br/>
### 3. 図表による解説

* ◆◆◆注意事項◆◆◆<br/> **以下の図表による解説は、特定の製品名を含んでいますが、これらはあくまでイメージしやすいようにサンプルとして表記したものであり、実際に動作する環境とは無関係です。したがって、これらの情報を元に実際の環境を構築する際の参考にすることは推奨されません。具体的な環境構築については、各製品の公式ドキュメンテーションや専門家のアドバイスを参照してください。**

#### 3.1. 構成図

```mermaid
graph LR
    A[ローカルPC] -->|PCIe| B[NVIDIA RTX 4090]
    A -->|PCIe| C[Intel Core i9]
    A -->|メモリバス| D[16 GB DDR5 RAM]
    A -->|SATA| E[Samsung 1 TB SSD]
    B -->|VRAM| F[16 GB NVIDIA RTX 4090 VRAM]
    
    subgraph AMD Ryzen 9 5900X
        G[AMD Ryzen 9 5900X] -->|メモリコントローラ| H[64 GB Unified Memory]
    end
```

#### 3.2. 関係図

```mermaid
graph LR
    A[ローカルLLM] -->|依存| B[ハードウェア要件]
    B -->|CPU| C[Intel Core i9]
    B -->|GPU| D[NVIDIA RTX 4090]
    B -->|RAM| E[16 GB DDR5 RAM]
    B -->|Storage| F[Samsung 1 TB SSD]
    A -->|API| G[NVIDIA CUDA]
    A -->|アプリケーション| H[Hugging Face Transformers]

```

#### 3.3. フロー図

```mermaid
sequenceDiagram
    ユーザー->>+アプリケーション: テキスト入力
    activate アプリケーション
    アプリケーション->>+ローカルLLM: テキスト入力
    activate ローカルLLM
    ローカルLLM->>+GPU or CPU: 計算
    activate GPU or CPU
    alt GPU
        GPU->>+VRAM: メモリアクセス
        activate VRAM
        VRAM-->>-GPU: データ
        deactivate VRAM
    else CPU
        CPU->>+RAM: メモリアクセス
        activate RAM
        RAM-->>-CPU: データ
        deactivate RAM
    end
    GPU or CPU-->>-ローカルLLM: 計算結果
    deactivate GPU or CPU
    ローカルLLM->>+アプリケーション: 出力生成
    deactivate ローカルLLM
    アプリケーション-->>-ユーザー: テキスト出力
    deactivate アプリケーション
```


<br/><br/>
### 4. まとめ

ローカルLLMの動作環境は、目的や予算、利用可能な資源に応じて最適な構成を選択することが重要です。
本資料が、読者のローカルLLM環境構築の一助となれば幸いです。

```mermaid
graph LR
    A[適切な動作環境の選択] -->|影響| B[ローカルLLMの性能向上]
```

<br/><br/>



