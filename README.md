# Supervoxel for 3D point clouds

## Introduction
We present a simple but effective supervoxel segmentation method for point clouds, which formalizes supervoxel segmentation as a subset selection problem. We develop an heuristic algorithm that utilizes local information to efficiently solve the subset selection problem. The proposed method can produce supervoxels with adaptive resolutions, and dose not rely the selection of seed points. The method is fully tested on three publicly available point cloud segmentation benchmarks, which cover the major point cloud types. The experimental results show that compared with the state-of-the-art supervoxel segmentation methods, the supervoxels extracted using our method preserve the object boundaries and small structures more effectively, which is reflected in a higher boundary recall and lower under-segmentation error.

The details can be found in the following [ISPRS 2018 paper](https://www.sciencedirect.com/science/article/pii/S0924271618301370)

## Citing our work
If you find our works useful in your research, please consider citing:

	Lin Y, Wang C, Zhai D, et al. Toward better boundary preserved supervoxel segmentation for 3D point clouds. Isprs Journal of Photogrammetry & Remote Sensing, 2018.
   
## Install & complie

Please directly copy the code into your workspace and complie it with any complier that supports C++11. It dose not require linking any additional libraries.

## Sample usage:
	cl::geometry::point_cloud::SupervoxelSegmentation(points, neighbors, resolution, metric, &supervoxels, &labels);

Where, 'points' is the source points, it can be read from XYZ file by calling: 
	cl::geometry::io::ReadXYZPoints(filename.c_str(), &points);

'neighbors' gives the neighborhood for each points. It can be constrcuted by compute k-neareast neighbors of each point. For example:

	const int k_neighbors = 15;
	cl::Array<cl::Array<int> > neighbors(n_points);
    cl::Array<cl::RPoint3D> neighbor_points(k_neighbors);
    for (int i = 0; i < n_points; ++i) {
        kdtree.FindKNearestNeighbors(kdtree.points()[i], k_neighbors, &neighbors[i]);
	}
	
'resolution' is the used to determine the number of supervoxels you want.
'metric' is used to evaluate the feature distance between two points. In our paper, we use the following metric, which is same to the VCCS.
	
	class VCCSMetric {
	public:
		explicit VCCSMetric(double resolution)
			: resolution_(resolution) {}

		double operator() (const PointWithNormal& p1,
						   const PointWithNormal& p2) const {
			return 1.0 - std::fabs(p1.normal * p2.normal) +
				   cl::geometry::Distance(p1, p2) / resolution_ * 0.4;
		}

	private:
		double resolution_;
	};
	
The output 'supervoxels' is an array that stores the indices of the representation points. 
And 'labels' is used to denote which supervoxel owns the i-th point.
	
Please see main.cc for more details.

## Contact

Please feel free to leave suggestions or comments to Yangbin Lin (yblin@jmu.edu.cn), or Wang Cheng (cwang@xmu.edu.cn)