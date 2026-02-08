# Humanoid Senior Perception Engineer — Interview Prep Checklist

Organized by likely interview areas. See also: [Master Study Plan](study_plan.md)

---

## ML / Computer Vision Deep Dive

### Detection & segmentation
- [ ] Review object detection: YOLO (v3-v8), Faster R-CNN, SSD, anchor-free detectors ([notes](../topics/ml_system_design/computer_vision.md))
- [ ] Review semantic segmentation: U-Net, DeepLab v3+, FCN ([notes](../topics/ml_fundamentals/semantic_segmentation.md), [lecture](../topics/ml_system_design/semantic_segmentation_lecture.md))
- [ ] Study instance segmentation: Mask R-CNN, panoptic segmentation (no notes yet)
- [ ] Study 3D object detection: PointNet, PointPillars, BEVFusion (no notes yet)

### Vision architectures
- [ ] Review CNN architectures: ResNet, EfficientNet, MobileNet ([notes](../topics/ml_fundamentals/neural_networks.md))
- [ ] Review Vision Transformers (ViT) ([notes](../topics/ml_fundamentals/visual_transformers.md))
- [ ] Review Swin Transformer ([notes](../topics/ml_fundamentals/swin_transformer.md))
- [ ] Review spatial transformers ([notes](../topics/ml_fundamentals/spatial_transformers.md))
- [ ] Study DL for CV lecture notes ([lectures](../resources/lectures/dl_for_cv/))

### Core ML
- [ ] Review training pipeline: loss functions, optimizers, learning rate schedules ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review evaluation metrics for detection: mAP, IoU, precision-recall curves ([notes](../topics/ml_fundamentals/deep_learning.md))
- [ ] Review contrastive and self-supervised learning ([notes](../topics/ml_fundamentals/contrastive_learning.md))
- [ ] Review attention mechanism ([notes](../topics/ml_fundamentals/attention_mechanism.md))

---

## Tracking & Sensor Fusion

### Object tracking
- [ ] Review multi-object tracking: SORT, DeepSORT, ByteTrack ([notes](../topics/ml_system_design/object_tracking_lecture_1.md), [lecture 2](../topics/ml_system_design/object_tracking_lecture_2.md))
- [ ] Review MOT metrics: MOTA, MOTP, IDF1, HOTA (no notes yet)
- [ ] Review MOT and density filtering ([notes](../topics/ml_system_design/mot_density_filtering.md))
- [ ] Study Kalman filter and extended Kalman filter for state estimation (no notes yet)

### Sensor fusion
- [ ] Study camera-LiDAR fusion approaches: early, mid, late fusion (no notes yet)
- [ ] Study IMU integration for ego-motion estimation (no notes yet)
- [ ] Study depth estimation: stereo vision, monocular depth (no notes yet)
- [ ] Review camera calibration: intrinsics, extrinsics, distortion ([notes](../topics/ml_system_design/computer_vision.md))

### Pose estimation
- [ ] Study human pose estimation: OpenPose, HRNet, ViTPose (no notes yet)
- [ ] Study hand/body pose for manipulation tasks (no notes yet)
- [ ] Study 6-DoF object pose estimation (no notes yet)

---

## Systems & Embedded Deployment

- [ ] Study model optimization: pruning, quantization (INT8/INT4), knowledge distillation (no notes yet)
- [ ] Study inference frameworks: TensorRT, ONNX Runtime, OpenVINO (no notes yet)
- [ ] Study edge deployment: Jetson, embedded GPU constraints (no notes yet)
- [ ] Study real-time inference: latency budgets, pipelining, async processing (no notes yet)
- [ ] Study ROS2 basics for robotics perception pipelines (no notes yet)
- [ ] Review PyTorch model export: TorchScript, ONNX ([notes](../resources/frameworks/pytorch/main_notes.md))

---

## Research Discussion (Diffusion, Imitation Learning)

- [ ] Review diffusion models vs autoregressive approaches ([notes](../topics/ml_fundamentals/diffusion_vs_autoregression.md))
- [ ] Review diffusion course notes ([notes](../resources/lectures/diffusion_course/01_introduction.md), [general](../resources/lectures/diffusion_course/general_notes.md))
- [ ] Study diffusion for robotics: action diffusion, trajectory generation (no notes yet)
- [ ] Study imitation learning: behavioral cloning, DAgger, inverse RL (no notes yet)
- [ ] Study sim-to-real transfer for perception (no notes yet)
- [ ] Review image generation and generative models ([notes](../topics/ml_fundamentals/image_generation.md))
- [ ] Review Humanoid perception study guide ([notes](../topics/ml_system_design/humanoid_perception_study_guide.md))
- [ ] Review GPT-4o tutorial notes for Humanoid prep ([notes](../resources/tutorials/humanoid_gpt4o_notes.md))

---

## Coding

- [ ] Solve 10 medium LeetCode problems (arrays, trees, graphs) ([leetcode tracker](../topics/coding/leetcode/README.md))
- [ ] Practice implementing CV algorithms from scratch (NMS, IoU, convolution)
- [ ] Review Python tips ([notes](../topics/coding/python_tips.md))
- [ ] Review NumPy/PyTorch tensor operations ([notes](../resources/frameworks/pytorch/tensor_operations.md))
