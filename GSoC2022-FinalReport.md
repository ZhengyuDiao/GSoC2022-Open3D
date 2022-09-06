# Tensor-based geometry improvements - Final Report

## Summary

This project was aimed to improve tensor-based geometry in Open3D. During the project, I implemented multiple geometry creation APIs and improved the performance of point cloud and triangle mesh  I/O, which resulting 5 PRs, totaling more than 2000 lines of code, were merged into master branch.

## Work done

### 1. Add multiple geometry creation APIs ([PR#5263](https://github.com/isl-org/Open3D/pull/5190), [PR#5294](https://github.com/isl-org/Open3D/pull/5294))

I implemented  _TriangleMeshFactory.cpp_ to support multiple geometry creation APIs in tensor-based module. In addition, I also added unit tests corresponding to each function. Now The tensor-based geometry creation APIs have reached feature parity with the old ones. All functions implemented are as follows,

- _create_box_
- _create_cone_
- _create_arrow_
- _create_coordinate_frame_
- _create_cylinder_
- _create_icosahedron_
- _create_mobius_
- _create_octahedron_
- _create_sphere_
- _create_tetrahedron_
- _create_torus_

In consideration of saving time, `create_box` was implemented according to the definition of the box and other creation APIs were implemented by `fromLegacy` function.  

### 2. Improve point cloud I/O ([PR#5404](https://github.com/isl-org/Open3D/pull/5404), [PR#5448](https://github.com/isl-org/Open3D/pull/5448))

Compared with legacy module, the tensor-based point cloud I/O support was limited before. I implemented _FileTXT.cpp_ to reach the feature of reading and writing TXT-like format files, including XYZ, XYZI, XYZN, XYZRGB files.   

There are two functions `ReadPointCloudFromTXT` and `WritePointCloudToTXT` in _FileTXT.cpp_. In `ReadPointCloudFromTXT` function, I used a line buffer to get the data of each line in the file and copied it directly into the constructed point cloud, which is highly efficient. The same operation  goes for another function.

In order to be consistent with tensor-based creation APIs, I also modified the default data type from _double_ to _float_ in point cloud I/O module.

### 3. Fix textures loading of GLB format ([PR#5443](https://github.com/isl-org/Open3D/pull/5443))

The glb-file is a fairly new format which was introduced only in 2015 in order to represent GLTF files in a binary format. It can store 3D data including scenes, models, lighting, material. Previously, Open3D was not able to load the embedded textures in GLB files correctly. I updated `LoadTextures` function in legacy FileASSIMP.cpp and fix this problem.

Specifically, I used `scence::GetEmbeddedTexture` to get the embedded texture in the GLB file. Next I implemented `io::CreateImageFromMemory` function to create the texture image from buffer, which supports PNG and JPG format. In addition, I added two example model `AvocadoModel` and `DamagedHelmetModel`  as unit tests.

### 4. Improve triangle mesh I/O ([PR#5415](https://github.com/isl-org/Open3D/pull/5415))

In the tensor-based triangle mesh I/O part, I took over the PR of a colleague and improved the code part. Specifically, I directly did a memory copy of the whole vertex instead of loop assignment.

## Future Work

### 1. Surface reconstruction for triangle mesh

Surface reconstruction is an important and well studied problem in computer graphics. Currently, Open3D supports some classical methods with legacy Eigen-based geometry. To enhance the functionality of tensor-based surface reconstruction algorithms, these functions need to be wrapped are listed below:

- _create_from_point_cloud_alpha_shape_
- _create_from_point_cloud_ball_pivoting_
- _create_from_point_cloud_poisson_

### 2. Geometry sampling for triangle mesh and point clouds

Geometry sampling is a basic functionality for 3D data processing libraries, including uniform sampling, decimation methods and clustering. In Open3D these functions of geometry sampling need to be wrapped are listed below:

- _cluster_dbscan_
- _sample_points_uniformly_
- _sample_points_poisson_disk_

## Acknowledgements

I would like to thank my mentor, Yixing for the constant support he provided. We always kept the regular and smooth communication during the project. This is a wonderful experience and I really appreciate him for a lot of what I learned along the way. Also, thank you to everyone in Open3D who helped me improve the project.







