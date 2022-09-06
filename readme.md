# Open3D GSoC 2022 Final Report (Zhengyu Diao)

## Summary

This project aims to improve tensor-based geometry in Open3D. During the project, I implemented multiple geometry creation APIs and improved the performance of point cloud and triangle mesh I/O, which resulted in 5 PRs, totaling more than 2000 lines of code. These changes have been merged into the master branch.

## Work done

### 1. Add multiple geometry creation APIs ([PR #5263](https://github.com/isl-org/Open3D/pull/5190), [PR #5294](https://github.com/isl-org/Open3D/pull/5294))

I implemented  `cpp/open3d/t/geometry/TriangleMeshFactory.cpp` to support multiple geometry creation APIs in tensor-based module. In addition, I also added unit tests corresponding to each function. Now The tensor-based geometry creation APIs have reached feature parity with the old ones. All functions implemented are as follows (in the `open3d::t::geometry` namespace):

- `TriangleMesh::create_box`
- `TriangleMesh::create_cone`
- `TriangleMesh::create_arrow`
- `TriangleMesh::create_coordinate_frame`
- `TriangleMesh::create_cylinder`
- `TriangleMesh::create_icosahedron`
- `TriangleMesh::create_mobius`
- `TriangleMesh::create_octahedron`
- `TriangleMesh::create_sphere`
- `TriangleMesh::create_tetrahedron`
- `TriangleMesh::create_torus`

In consideration of saving time, `TriangleMesh::create_box` was implemented according to the definition of the box and other creation APIs were implemented by `TriangleMesh::FromLegacy` function.

### 2. Improve point cloud I/O ([PR #5404](https://github.com/isl-org/Open3D/pull/5404), [PR #5448](https://github.com/isl-org/Open3D/pull/5448))

Compared with the legacy module, the tensor-based point cloud I/O support was limited before. I implemented `cpp/open3d/t/op/file_format/FileTXT.cpp` to reach the feature of reading and writing TXT-like format files, including XYZ, XYZI, XYZN, XYZRGB files.

There are two functions `io::ReadPointCloudFromTXT` and `io::WritePointCloudToTXT` in `FileTXT.cpp`. In the `io::ReadPointCloudFromTXT` function, I used a line buffer to get the data of each line in the file and copied it directly into the constructed point cloud, which is highly efficient. The same operation goes for another function.

In order to be consistent with tensor-based creation APIs, I also modified the default data type from _double_ to _float_ in the point cloud I/O module.

### 3. Fix textures loading of GLB format ([PR #5443](https://github.com/isl-org/Open3D/pull/5443))

The glb-file is a fairly new format that was introduced only in 2015 in order to represent GLTF files in a binary format. It can store 3D data including scenes, models, lighting, and material. Previously, Open3D was not able to load the embedded textures in GLB files correctly. I updated `LoadTextures` function in `cpp/open3d/io/file_format/FileASSIMP.cpp` and fix this problem.

Specifically, I used `scence::GetEmbeddedTexture` to get the embedded texture in the GLB file. Next, I implemented the `io::CreateImageFromMemory` function to create the texture image from buffers, which supports PNG and JPG formats. In addition, I added two example models `AvocadoModel` and `DamagedHelmetModel` as unit tests.

### 4. Improve triangle mesh I/O ([PR #5415](https://github.com/isl-org/Open3D/pull/5415))

In the tensor-based triangle mesh I/O part, I took over the PR of a colleague and improved the code part. Specifically, I directly did a memory copy of the whole vertex instead of a loop assignment.

## Future Work

### 1. Surface reconstruction for triangle mesh

Surface reconstruction is an important and well-studied problem in computer graphics. Currently, Open3D supports some classical methods with legacy Eigen-based geometry. To enhance the functionality of tensor-based surface reconstruction algorithms, we need to wrap the following functions for tensor-based geometry:

- `create_from_point_cloud_alpha_shape`
- `create_from_point_cloud_ball_pivoting`
- `create_from_point_cloud_poisson`

### 2. Geometry sampling for triangle mesh and point clouds

Geometry sampling is basic functionality for 3D data processing libraries, including uniform sampling, decimation methods and clustering. We need to wrap the following functions for tensor-based geometry:

- `cluster_dbscan`
- `sample_points_uniformly`
- `sample_points_poisson_disk`

## Acknowledgements

I would like to thank my mentor, Yixing for the constant support he provided. We always kept regular and smooth communication during the project. This is a wonderful experience and I really appreciate him for a lot of what I learned along the way. Also, thank you to everyone in Open3D who helped me improve the project.
