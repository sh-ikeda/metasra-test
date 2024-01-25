## ファイルの説明
- `chip-atlas_vocab_metasra.tsv`  
  ChIP-Atlas の cell type class の文字列を MetaSRA でオントロジーに当てた結果。

- `chip-atlas_srx_biosample.tsv`  
  ChIP-Atlas の celltypeList.tab で生物種が hg38 の各行から、SRA Experiment の ID を 1 個ずつ抜き出したものと BioSample ID の対応。

- `chip-atlas_biosample_metasra.tsv`  
  前項の BioSample を入力として MetaSRA を実行した結果。

- `chip-atlas_vocab_metasra_not_in_direct.tsv`  
  `chip-atlas_vocab_metasra.tsv` にある Cellosaurus (CVCL) にマップされた行のうち、`chip-atlas_biosample_metasra.tsv` に存在しないもの。つまり、直接 BioSample を入力した場合はマップされないが、ChIP-Atlas の cell type class への名寄せを経由するとマップされたもの。

`$ awk -F "\t" -v OFS="\t" 'FNR==1{f++}f==1&&$4~/CVCL:/{a[$1,$4]=1}f==2{b[$1]=$2}f==3&&$4~/CVCL:/&&!a[b[$1],$4]{print $0, b[$1]}' chip-atlas_biosample_metasra.tsv chip-atlas_srx_biosample.tsv chip-atlas_vocab_metasra_test.tsv > chip-atlas_vocab_metasra_not_in_direct.tsv`

## 補足: MetaSRA の出力

| column | example    | description                            |
|--------|------------|----------------------------------------|
| 1      | SRX3751779 | 入力の ID                              |
| 2      | cell_type  | 入力の属性の key                       |
| 3      | 1765-92    | 入力の属性の value                     |
| 4      | CVCL:S817  | マップされたオントロジータームの ID    |
| 5      | 1765-92    | マップされたオントロジータームのラベル |
| 6      | False      | Consequent か                          |
| 7      | True       | 全長マッチか                           |
| 8      | True       | 完全マッチか                           |
| 9      | TERM_NAME  | 何にマッチしたか (label か synonym か)   |

マップされなかった属性は 4 列目以降が空。
