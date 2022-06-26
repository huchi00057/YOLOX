建立虛擬環境
====
    conda create –name yolox python=3.9
    conda activate yolox

安裝YOLOX (下載YOLOX的github)
====
方法一
==
    git clone https://github.com/Megvii-BaseDetection/YOLOX.git
    cd YOLOX
    pip install -U pip && pip install -r requirements.txt
    pip install -v -e . # or python setup.py develop
方法二
==
如果不想用指令進行下載，可手動下載並解壓縮
> https://github.com/Megvii-BaseDetection/YOLOX

然後再安裝requirement.txt(還不是要用指令XD)

⚠⚠⚠Pytorch、torchversion 要用GPU跑的話，套件要特別去下載
====
![image](https://user-images.githubusercontent.com/46515944/175810603-3918ae61-68d1-4640-ac7b-ed7e0aabcf71.png)

再回到視窗輸指令進行下載

    pip install torch-1.7.1+cu110-cp39-cp39-win_amd64.wh 


安裝pycocotools
====
    pip install cython; pip install 'git+https://github.com/cocodataset/cocoapi.git#subdirectory=PythonAPI'
    
下載預訓練模型
====
    wget https://github.com/Megvii-BaseDetection/YOLOX/releases/download/0.1.1rc0/yolox_s.pth
    
以及下載yolox_s.pt，而且要放在yolox的資料夾內
> https://github.com/Megvii-BaseDetection/YOLOX/releases/download/0.1.1rc0/yolox_s.pth

測試
====
    python tools/demo.py image -f exps/default/yolox_s.py -c yolox_s.pth --path assets/dog.jpg --conf 0.25 --nms 0.45 --tsize 640 --save_result --device [gpu]
    
> *  -f 後面放參數設定的檔案，這邊使用官方的預設參數 exps/default/yolox_s.py
> *  -d GPU的數量
> *  -b batch size的值，通常建議gpu*8  
> (前人實測資料：8GPU跑5epoch，43分鐘；2GPU跑5eopch，2小時16分鐘)
> *  -c 後面放預測時使用的 weight 檔，這邊使用官方的預訓練模型 yolox_s.pth
> *  --path 後面放你要預測的圖片路徑，也可以放想要預測的資料夾路徑，這邊使用官方提供的照片 assets/dog.jpg
> *  --conf 後面放 confidence 的 threshold，這邊使用 0.25
> *  --nms 後面放非極大值抑制 (Non-Maximum Suppression) 的 threshold，這邊使用 0.45
> *  --tsize 後面放你要將影像 resize 成多大丟進模型裡預測，這邊使用 640
> *  --save_result 如果要將預測結果存下來可以加這個
> *  --device 要用什麼 device 預測，可選 cpu 或 gpu，這邊使用 gpu

![image](https://user-images.githubusercontent.com/46515944/175810698-d8b895a1-178d-4ada-a9b7-4b39f9ec95a6.png)

![image](https://user-images.githubusercontent.com/46515944/175810416-4fe84b0c-dbe6-4826-a440-c7f68de7b9a0.png)

調整參數
====
> (一)	在 YOLOX/exps/example/custom/yolox_s.py 中，修改資料夾路徑 self.data_dir 和類別數量 self.num_classes

> (二)	在 YOLOX/yolox/data/datasets/coco_classes.py 中的類別改成自己對應的類別，例如這邊我改成 car


訓練
====
    python tools/train.py -f exps/example/custom/yolox_s.py -d 0 -b 4 --fp16 -o -c yolox_s.pth
    
斷掉接續訓練
===
    python tools/eval.py -n yolox-s -c YOLOX_outputs/yolox_voc_s/latest_ckpt.pth -b 8 -d 1 --conf 0.001 -f exps/example/yolox_voc/yolox_voc_s.py
> *  -n：指定型號
> *  -c：最後訓練的重量



遭遇Bug之解決
===
    Traceback (most recent call last):
      File "D:\yolox\tools\demo.py", line 14, in <module>
        from yolox.data.data_augment import ValTransform
    ModuleNotFoundError: No module named 'yolox'
  
 ![image](https://user-images.githubusercontent.com/46515944/175810808-dba0aa76-0c08-4d56-a34d-b58b50c11ae0.png)
 
 
在它說的那個檔案處加入

    import sys
    sys.path.append(r' D:\yolox')
    
![image](https://user-images.githubusercontent.com/46515944/175810825-debc5211-26a5-4e94-8ce6-0ada4cc62088.png)

