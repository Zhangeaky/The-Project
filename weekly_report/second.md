### PointNet

```python
物体分类：
语义分割：
```

- 点云语义分割数据集

  ```
  数据集解压后里面由ply_data_all_0.h5 至ply_data_all_23.h5 共24个h5格式的数据文件，每个里面都有两个dataset：data 和 label（每一个点对应的标签）。这24个文件共有23×1000+585=23585行，前23个文件有1000行，第24个文件有585行，每行一个Block，每个Block有4096个点，每点9个维度。
  
  另外还有两个txt文件：all_files.txt 和 room_filelist.txt。前者保存24个数据文件名。后者有23585 行; 即对应每个Block是在哪个area的哪个room采集的
  
  1.使用H5DF数据加载
  2.使用自己的数据格式加载，修改代码
  ```

  

`参考：`

https://www.twblogs.net/a/5bd35c7e2b717778ac202505/?lang=zh-cn



