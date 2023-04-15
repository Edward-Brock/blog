# 使用 Python 进行简单的人脸识别

通过使用 Python 进行简单的人脸识别非常简单，使用到了 OpenCV 库内的 [haarcascade\_frontalface\_default.xml](https://github.com/opencv/opencv/blob/master/data/haarcascades/haarcascade_frontalface_default.xml) ，haarcascade 包含经过训练的用于检测对象的特定类型的分类器，例如：人脸，人眼，倾斜的人脸，鼻子，口嘴，微笑，上半身，下半身等。

## 代码

```python
import cv2
import os


def load_img(path, name, mun=30, add_with=0):
    if not os.path.exists(path):
        os.mkdir(path)
    # 获取人脸识别模型
    classfier = cv2.CascadeClassifier('haarcascade_frontalface_default.xml文件路径')
    # 创建一个窗口
    cv2.namedWindow('Face')
    # 使用内置摄像头
    cap = cv2.VideoCapture(0)
    # 使用视频进行识别
    # cap = cv2.VideoCapture('视频路径')
    # 根据摄像头设置IP及rtsp端口
    # url='rtsp://admin:admin@192.168.0.104:8554/live'
    # cap = cv2.VideoCapture(url)
    i = 0  # 计数
    if cap.isOpened():
        while i < mun:
            ok, frame = cap.read()  # 读取一帧图片
            if not ok:
                continue

            faces = classfier.detectMultiScale(frame, 1.3, 5, minSize=(32, 32))

            if len(faces) > 0:
                for face in faces:
                    x, y, w, h = face

                    img = frame[y - add_with:y + h + add_with, x - add_with:x + w + add_with]
                    # 显示人脸框
                    cv2.rectangle(frame, (x - add_with, y - add_with), (x + w + add_with, y + h + add_with),
                                  (0, 255, 0), 2)
                    save_path = path + '/' + name + '_' + str(i) + '.jpg'
                    print(save_path)
                    img2 = cv2.resize(img, (112, 112))
                    cv2.imwrite(save_path, img2)
                    i += 1

            cv2.imshow('face', frame)
            c = cv2.waitKey(10)
            if c & 0xFF == ord('q'):
                break

        cap.release()
        cv2.destroyAllWindows()


if __name__ == '__main__':
    # 第一个参数为保存图片的路径
    # 第二个参数为保存图片名字的开头
    # 第三个参数为图片的数量
    # 第四个参数可以调节图片的大小
    load_img('保存图片文件夹路径', 'lq', 500, 0) 
```
