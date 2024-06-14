# photo-app-quickstart
import cv2
import numpy as np

def beautify_face(image_path, output_path):
    # 读取图像
    image = cv2.imread(image_path)
    if image is None:
        print("Error: Unable to load image.")
        return

    # 转换为HSV颜色空间
    hsv_image = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)

    # 创建皮肤掩码
    lower_skin = np.array([0, 20, 70], dtype=np.uint8)
    upper_skin = np.array([20, 255, 255], dtype=np.uint8)
    skin_mask = cv2.inRange(hsv_image, lower_skin, upper_skin)

    # 应用双边滤波器
    smoothed_image = cv2.bilateralFilter(image, d=15, sigmaColor=75, sigmaSpace=75)

    # 将平滑后的区域应用到原图像
    result = cv2.bitwise_and(smoothed_image, smoothed_image, mask=skin_mask)
    non_skin_mask = cv2.bitwise_not(skin_mask)
    result += cv2.bitwise_and(image, image, mask=non_skin_mask)

    # 保存结果
    cv2.imwrite(output_path, result)
    print(f"Beautified image saved to {output_path}")

# 示例用法
beautify_face('input.jpg', 'output.jpg')
