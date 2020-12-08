### PCL基础

基础数据类型

-  

  ```c++
  //点云对象
  pcl::PointCloud<T>
  
  
  pcl::PointXYZ
  //
  pcl::PointXYZI
  //带有颜色信息的点
  pcl::PointXYZRGB
  pcl::InterestPoint-float
  
  //法线方向和曲率测量值
  pcl::Normal-float
  ```

- ros点云数据和pcl点云数据的转化

  ```c++
  ros点云数据：sensor_msgs/pointcloud2
  pcl点云数据：pcl::PointCloud<T>
  
  //转化函数,位于pcl_conversions 功能包中
  //PointCloud格式转化为Sensor_msgs
  pcl::toROSMsg( ... ，sensor_msgs::PointCloud2)
  
  //将PointCloud类型的sensor_msg 转化为PCL点云对象
  pcl::fromROSMsg()
  ```

- 保存PCD文件到本地

  ```C++
  
  pcl::io::savePCDFileASCII ("存储路径", *msgptr); 
  ```

- 加载本地PCD 文件

  ```c++
  //声明PointCloud数据类型的指针
  pcl::PointCloud<pcl::PointXYZRGB>::Ptr pcl_clr_ptr(new pcl::PointCloud<pcl::PointXYZRGB>);
  //根据声明的指针将PCD文件生成相应的对象模型
  pcl::io::loadPCDFile<pcl::PointXYZRGB>(fname, *pcl_clr_ptr)
  ```

### 点云分割（目标检测）

- RanSac(采样一致性算法)

  ```
  地物分割
  ```
  
- 点云去平面

- 深度学习

  ```
  在复杂场景中简单的传统方法对点云进行语义分割难度太大，使用深度学习可能会比较方便
  点云数据集：
ModelNet 
  ShapeNet
  ```
  
  
  
  