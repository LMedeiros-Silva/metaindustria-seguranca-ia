# Third-party notices

## OpenCV

The `opencv-python-headless` dependency is distributed under the OpenCV license. OpenCV
4.5.0 and newer are licensed under Apache License 2.0.

- Project: https://opencv.org/
- License information: https://opencv.org/license/

## YuNet face detector

The configured `face_detection_yunet_2023mar.onnx` artifact is obtained from the official
OpenCV Zoo YuNet directory. The directory declares its files under the MIT License.

- Source and license: https://github.com/opencv/opencv_zoo/tree/main/models/face_detection_yunet

## SFace face recognizer

The configured `face_recognition_sface_2021dec.onnx` artifact is obtained from the official
OpenCV Zoo SFace directory. The directory declares its files under Apache License 2.0.

- Source and license: https://github.com/opencv/opencv_zoo/tree/main/models/face_recognition_sface

The model files are installation artifacts and are intentionally ignored by Git. Use
`python scripts/download_face_models.py` to download the pinned artifacts and verify their
SHA-256 checksums.

## Ultralytics YOLOv8 and PPE checkpoint

The PPE detector uses `ultralytics-opencv-headless` 8.4.115, matching the runtime version stored
in the provided custom YOLOv8 checkpoint. The checkpoint metadata declares `AGPL-3.0`.

Academic copy (Sprint 4): the retrained `models/ppe/best.pt` versioned in this repository has
SHA-256 `961AB27019BADAE6E2F952BD633E655EA1D58F708801A9A4B531CD39EB578B42`.
The original 2Identify notice listed
`73A87F86E68F7C5091857F48B55BB756A70B88D0217CC35C58EED3969C7EBA20` for an earlier checkpoint.

- Documentation: https://docs.ultralytics.com/
- License guidance: https://www.ultralytics.com/license

Ultralytics states that proprietary or commercial deployment requires an appropriate commercial
license unless the complete project is distributed under the AGPL-3.0 terms. Licensing must be
resolved before commercial distribution. In the original 2Identify repository the checkpoint is
ignored by Git; this academic copy versions it for reproducible evaluation.

## Ultralytics YOLO11n-pose

The ergonomic screening pipeline uses the public `yolo11n-pose.pt` COCO pose checkpoint from
the official Ultralytics assets release. The installation script verifies SHA-256:

`869E83FCDFFDC7371FA4E34CD8E51C838CC729571D1635E5141E3075E9319DC0`

- Model documentation: https://docs.ultralytics.com/models/yolo11/
- Pose documentation: https://docs.ultralytics.com/tasks/pose/
- Artifact: https://github.com/ultralytics/assets/releases/download/v8.4.0/yolo11n-pose.pt

The checkpoint is ignored by Git and is installed with `python scripts/download_pose_model.py`.
The same Ultralytics licensing review applies before distribution or commercial deployment.

