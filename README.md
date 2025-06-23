

---

# YOLOv11n FP16 Inference & Entry/Exit Counting

This repository provides an FP16-quantized YOLOv11n model and an inference script that counts vehicle entries/exits across configurable lines.

## 📁 Structure

```
yolov11n/
├── dataset/                  
│   └── test.mp4              # your input video(s)
├── outputs/
│   └── detected_cars_fp16.mp4
├── quantized_model/
│   └── best_fp16.pth         # FP16-only state_dict
├── FRAMES/                   # per-frame debug outputs
├── quantize_model.py         # produces best_fp16.pth from best.pt
├── infer_count.py            # runs FP16 inference + counting
├── requirements.txt
└── README.md
```

## ⚙️ Setup

1. **Create & activate** your env:

   ```bash
   conda create -n yolov11n python=3.9 -y
   conda activate yolov11n
   pip install -r requirements.txt
   ```

2. **(Crash workaround)**
   If the kernel dies under Windows, insert at top of `infer_count.py`:

   ```python
   import os
   os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"
   print("Set KMP_DUPLICATE_LIB_OK =", os.environ["KMP_DUPLICATE_LIB_OK"])

   ```

## 🚀 Inference & Counting

1. **Quantize your trained model**

   ```bash
   python quantize_model.py \
     --input outputs/cars_cosine_run2/weights/best.pt \
     --output quantized_model/best_fp16.pth
   ```

   * **Input**: `best.pt` from training (`train_cars_cosine.py` → `outputs/cars_cosine_run2/weights/best.pt`)
   * **Output**: `quantized_model/best_fp16.pth`

2. **Run FP16 inference + count**

   ```bash
   python infer_count.py \
     --video dataset/test.mp4 \
     --model quantized_model/best_fp16.pth \
     --output outputs/detected_cars_fp16.mp4
   ```

   * **Red line** = entry counter
   * **Blue line** = exit counter
   * *TIP*: adjust `w, h, red_x_start/end, red_y_offset, blue_x_start/end, blue_y_offset` inside `infer_count.py`

3. **Results**

   * **Console**: total frames processed, FPS, entry/exit counts
   * **Video**: `outputs/detected_cars_fp16.mp4`
   * **Frames**: `FRAMES/` (one image per processed frame)

---
