# TSL 3D-ResNet
## 事先閱讀
- 3D-ResNet
	https://arxiv.org/abs/1711.09577
- Learn to pay attention
	https://arxiv.org/abs/1804.02391
- Squeeze-and-Excitation Networks
	https://arxiv.org/abs/1709.01507
- Selective Kernel Networks
	https://arxiv.org/abs/1903.06586
- Ranger
    https://github.com/lessw2020/Ranger-Deep-Learning-Optimizer

## Install
1. 安裝以下必須套件
```shell= !
$ pip install apng opencv-python Pillow PyYAML scikit-learn scipy tensorboard matplotlib
```
2. 依照當前環境安裝相符版本的 `torch` 和 `torchvision`
3. 安裝 [Ranger](https://github.com/lessw2020/Ranger-Deep-Learning-Optimizer)
```shell=
$ git clone https://github.com/lessw2020/Ranger-Deep-Learning-Optimizer
$ cd Ranger-Deep-Learning-Optimizer
```
由於我是使用較新版本的 torch，所以需要修改 `Ranger-Deep-Learning-Optimizer/ranger/__init__.py` 檔案
原始檔案如下
```python= !
from .ranger import Ranger
from .ranger913A import RangerVA
from .rangerqh import RangerQH
```
修改為
```python= !
from .ranger2020 import Ranger
from .ranger913A import RangerVA
from .rangerqh import RangerQH
```
接著安裝修改版本的 Ranger
```shell=
$ pip install -e .
```
> 建議到官網查看 https://github.com/lessw2020/Ranger-Deep-Learning-Optimizer


## Codes & configs
如果 FTP 還健在應該可以到 http://msp.csie.ncu.edu.tw:9487/jimmy801/TSL_isolated 查看，程式碼部分都有註解，config 內容基本上參數都和程式碼中的參數相同

## Usage
### `main.py`
#### 參數
:::warning
可以使用 `python main.py --help` 或是 `python main.py -h` 查看參數
:::
##### 共用參數
- `-c`, `--config`
    設定配置的 yaml config file 路徑
- `--gpu`
    要使用的顯卡編號，若要使用多顯卡則以 `,` 區隔所有標號且***不可有空格***
- `--cpu`
    加了這個參數表示使用 cpu 而不用 gpu
##### train 參數
- `-pre_pth`, `--previous_path`
    之前訓練好的權重路徑，讀取該路徑權重並繼續訓練下去
    不加此參數則表示從頭開始訓練
- `-se`, `--start_epoch`
    起始訓練 epoch 數字
    不加此參數則表示從 0 開始
    :::danger
    標號從 0 開始，所以如果參數給 `-se 5` 表示會從第 6 個 epoch 開始標號
    :::
##### test 參數
- `--test`
    加此參數則表示是進行測試，不會寫 log file 之類的
    不加此參數則表示要訓練
- `-ckpt`, `--check_point`
    要測試的權重路徑，只測試一個權重
- `-ckpt_fmt`, `--check_point_format`
    如要一次測試不同 epoch 的多權重，且那些權重檔名只有差在標號部分則可以使用此參數並搭配以下兩個參數來做測試，此參數要是使用 python string format
- `ckpt_fmt_s`, `--check_point_format_start`
    搭配 `-ckpt_fmt` 參數使用，測試權重的 epoch 起始標號
- `-ckpt_fmt_e`, `--check_point_format_end`
    搭配 `-ckpt_fmt` 參數使用，測試權重的 epoch 終點標號
    若不加此參數則預設從起始標號直到該資料夾最後 epoch
- `-tc`, `--test_config`
    測試的 yaml config 檔案路徑

---

#### train
##### 從頭開始繼續訓練
```shell= !
$ python main.py -c config.yaml --gpu 1
```
##### 說明
model 是使用 `config.yaml` 的 config 作為設定的配置，並使用 1 號顯卡訓練

##### 從之前權重開始繼續訓練
```shell= !
$ python main.py -c config.yaml --gpu 0,1 -pre_pth save/checkpoint_epoch3.pth -se 3
```
##### 說明
model 是使用 `config.yaml` 的 config 作為設定的配置，並同時使用 0 號及 1 號顯卡訓練，接著是讀取上次訓練的權重，權重路徑為 `save/checkpoint_epoch3.pth`，此次訓練是由第 4 epoch 開始(-se 是從 0 開始算)

---

#### test
:::warning
測試寫得很亂，可能要在自己多研究並修改
:::
##### 測試指定權重
```shell= !
$ python main.py --test -c config.yaml --cpu -ckpt save/checkpoint_epoch003.pth
```
##### 說明
測試 model 並使用 `config.yaml` 的 config 作為設定的配置，測試 `save/checkpoint_epoch003.pth` 權重，且是使用 CPU 來測試

```shell= !
$ python main.py --test -c config.yaml -tc test_config.yaml -ckpt_fmt save/checkpoint_epoch{:03d}.pth -ckpt_fmt_s 6 -ckpt_fmt_e 101 --gpu 0
```
##### 說明
測試 model 並使用 `config.yaml` 的 config 作為設定的配置，測試 `save/checkpoint_epoch006.pth` 到 `save/checkpoint_epoch100.pth` 權重，且是使用 0 號顯卡來測試

---

### `dataset.py`
#### 參數
:::warning
可以使用 `python dataset.py --help` 或是 `python dataset.py -h` 查看參數
:::
- `-c`, `--config`
    使用 `config.yaml` 的 config 作為設定的配置
- `--type`
    使用 `config.yaml` 的 config 中的指定類型作為設定的配置，預設為 `train`
- `-v`, `--visualze_path`
    將 dataset 中的資料視覺化的資料夾路徑，預設為 `transform_visual`
- `--fps`
    視覺化影片或動圖的 fps，預設為 30
- `-f`, `--format`
    儲存的檔案格式，可以為 `apng`(或是也可以只寫 `png`)，`avi`，`gif`，`mp4`，預設為 `None`，表示不儲存
- `-sm`, `--save_mask`
    加上此參數表示將 mask 資料一併儲存
- `-vid`, `--video_id`
    只儲存指定編號的檔案
- `-vn`, `--video_name`
    設定檔案儲存的名稱，預設是和檔案 id 相同
- `-vnb`, `--video_name_begin`
    設定檔案儲存名稱的前綴
- `-vne`, `--video_name_end`
    設定檔案儲存名稱的後綴
- `-fm`, `--force_mask`
    忽略 config 中的 mask 機率，強制所有檔案要套用 mask
- `-fnm`, `--force_no_mask`
    忽略 config 中的 mask 機率，強制所有檔案不套用 mask

#### 視覺化
```shell= !
$ python dataset.py -c config.yaml -f avi -fm
```
##### 說明
使用 `config.yaml` 的 config 作為設定的配置，並強制套用 mask，將視覺化 avi 影片檔存在預設路徑 `transform_visual`
