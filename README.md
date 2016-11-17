Arrangement
===========
<!--- <logo src="https://github.com/saeedghsh/arrangement/blob/master/pyarrang.png" alt="none" width="50" height="50"> --->
This repository provides a python package for decomposition of a 2D plane over a set of curves.
Currently straight lines and circles are the supported classes of curves.

Dependencies and Download
-------------------------
- Dependencies
 * Python >=2.6
 * numpy >= 1.10.2
 * sympy >= 1.0
 * networkx >= 1.10
 * matplotlib >= 1.4.3
 * yaml >= 3.10 [optional]

- Installing Dependencies
```shell
% python2:
apt-get install python-numpy python-matplotlib python-sympy
pip install networkx pyyaml

% python3:
apt-get install python3-matplotlib python3-pip
pip3 install numpy sympy networkx pyyaml
```

- Download
```shell
git clone https://github.com/saeedghsh/arrangement/ && cd arrangement/src/
```


Basic Use and API
-----------------
- Basic Use
```python
# define curves:
import modifiedSympy as mSym
curves = [mSym.CircleModified( args=((i,i), 3) ) for i in range(4)]


# deploy arrangement
import arrangement as arr
arrang = arr.Arrangement(curves, multiProcessing=4)

# visualize the result
import plotting as myplt
myplt.animate_face_patches(arrang)
```
<!--- ![arrangement](https://github.com/saeedghsh/arrangement/blob/master/figures/arrangement.png)
<arrangement src="https://github.com/saeedghsh/arrangement/blob/master/figures/arrangement.png" alt="none" width="200" height="200"> --->
![animation](https://github.com/saeedghsh/arrangement/blob/master/figures/animation.gif)

- Storing curves in a yaml file.
A yaml file storing the curves should look like this:

```yaml
lines:
    - [x1,y1, x2,y2] or [x1,y1, slope]
segments:
    - [x1,y1, x2,y2]
rays:
    - [x1,y1, x2,y2]
circles:
    - [center_x, center_y, radius]
arcs:
    - [center_x, center_y, radius, interval_lower , interval_upper]
```

See examples of yaml files in [testCases](https://github.com/saeedghsh/arrangement/tree/master/src/testCases).
Use the script [loadFromYaml.py](https://github.com/saeedghsh/arrangement/blob/master/src/loadFromYaml.py) to retrieve data from a yaml file as following:
```python
from loadFromYaml import load_data_from_yaml
data = load_data_from_yaml( address+fileName )
curves = data['curves']
```


- Arrangement class Hierarchy (the figure is created by [Pyreverse](https://www.logilab.org/blogentry/6883))
![classes_arrangement](https://github.com/saeedghsh/arrangement/blob/master/figures/classes_arrangement.png)
<!--- <classes_arrangement src="https://github.com/saeedghsh/arrangement/blob/master/figures/classes_arrangement.png" alt="none" width="50" height="50"> --->

- Accessing nodes, edges and faces
```python
for nodeIdx in arrang.graph.nodes():
    print nodeIdx, ': ', arrang.graph.node[nodeIdx]['obj'].attributes

for halfEdgeIdx in arrang.graph.edges(keys=True):
    (s,e,k) = (startNodeIdx, endNodeIdx, path) = halfEdgeIdx
    print (s,e,k), ': ', arrang.graph[s][e][k]['obj'].attributes

for fIdx,face in enumerate(arrang.decomposition.faces):
    print fIdx, ': ', face.attributes
```

- Visualization, plotting nad animating
```python
myplt.plot_decomposition(arrang,
                         interactive_onClick=False, interactive_onMove=False,
                         plotNodes=True, printNodeLabels=True,
                         plotEdges=True, printEdgeLabels=True)

myplt.animate_face_patches(arrang, timeInterval = .5*1000)
```
<!--- ![arrangement](https://github.com/saeedghsh/arrangement/blob/master/figures/arrangement.png)
<arrangement src="https://github.com/saeedghsh/arrangement/blob/master/figures/arrangement.png" alt="none" width="50" height="50"> --->

<!--- <animation src="https://github.com/saeedghsh/arrangement/blob/master/figures/animation.png" alt="none" width="50" height="50"> --->

- Transformation example
```python
# arrang.transform_sequence('sequence', ( values, ), ( point, ) )
arrang.transform_sequence('T', ( (10,0), ), ( (0,0), ) )
arrang.transform_sequence('R', ( np.pi/2, ), ( (0,0), ) )
arrang.transform_sequence('S', ( (.2,.2), ), ( (0,0), ) )
arrang.transform_sequence('SRT', ((5,5), -np.pi/2, (-10,0), ),
                                 ((0,0), (0,0),    (0,0), ) )
```
<!--- ![translate](https://github.com/saeedghsh/arrangement/blob/master/figures/T.png)
<translate src="https://github.com/saeedghsh/arrangement/blob/master/figures/T.png" alt="none" width="50" height="50">
![rotate](https://github.com/saeedghsh/arrangement/blob/master/figures/R.png)
<rotate src="https://github.com/saeedghsh/arrangement/blob/master/figures/R.png" alt="none" width="50" height="50">
![scale](https://github.com/saeedghsh/arrangement/blob/master/figures/S.png)
<scale src="https://github.com/saeedghsh/arrangement/blob/master/figures/S.png" alt="none" width="50" height="50">
![SRT](https://github.com/saeedghsh/arrangement/blob/master/figures/SRT.png)
<SRT src="https://github.com/saeedghsh/arrangement/blob/master/figures/SRT.png" alt="none" width="50" height="50"> --->

<!---
- Checking sundivisions' intersection
```python
import copy
arrang_copy = copy.copy(arrang)
arrang_copy.transform_sequence('R', ( np.pi/2, ), ( (0,0), ) )
arrang_copy.transform_sequence('T', ( (-5,0), ), ( (0,0), ) )

arrang_copy = copy.copy(arrang)
print arrang.decomposition.does_intersect(arrang_new.decomposition)
print arrang.decomposition.does_overlap(arrang_new.decomposition)
print arrang.decomposition.does_enclose(arrang_new.decomposition)
```
--->

- Merging Faces
```python
# arrang.merge_faces([face_indices,])
arrang.merge_faces([2,3,4,5,6,7,8,9])
myplt.animate_face_patches(arrang)
```
![merge_faces](https://github.com/saeedghsh/arrangement/blob/master/figures/merge_faces.png)
<!--- <merge_faces src="https://github.com/saeedghsh/arrangement/blob/master/figures/merge_faces.png" alt="none" width="50" height="50"> --->

For more examples and details see the [demo.py](https://github.com/saeedghsh/arrangement/blob/master/src/demo.py).

<!--- 
Limitations, Bugs and TODO
--------------------------
This project is currently under development, please note that efficiency and optimallity have not been the objective of the implementation. As a consequence, the code at your disposal is not in its best shape. The objective of this implementation has been to show the concept and prototyping it.

- [ ] make the code python3 compatible
- [x] add to readme: yaml_parser() usage instructions
- [ ] Using [CGAL](cgal.org/):
 - [2D arrangement] (http://doc.cgal.org/latest/Arrangement_on_surface_2/index.html)
 - [halfEdge data structure] (http://doc.cgal.org/latest/HalfedgeDS/index.html)
 - [Handles and Circulators] (http://doc.cgal.org/latest/Circulator/classCirculator.html)
 - [python binding] (https://github.com/CGAL/cgal-swig-bindings/wiki/Package_wrappers_available)
- [ ] How to detect the overlap of two arcs?
- [ ] `Decomposition.does_intersect()`, `Decomposition.does_overlap()` and `Decomposition.does_enclose()` require the `Decomposition.superFace` not to be `None`. Which by default is!
- [ ] Include the boundary points of segments, rays and arcs as nodes so that a segment/ray/arc is full included in the graph.
- [ ] To implement: `svg_to_yaml_parser()`, `Arrangement.add_new_curves()` and `Arrangement.save_to_image()`.
- [ ] The problem with [`sympy.Circle.rotate()`] (https://github.com/sympy/sympy/issues/11743).
- [ ] `Arrangement.merge_faces()` recomputes the decomposition and hence resets face indices. This method is suitable to be called after the arrangement has been put in use, that is to say attributes have been assigned to faces. It does not reset the nodes and edge indices.
- [ ] When dealing with ray or segment (and Arc), a given point might not be on the object (out of the interval). That's why I should always check if `object.contians(point)` this appears in IPE, DPE,... so, whenever using those, make sure to consider the cases where these methods might return False instead of expected type.
- [ ] When a node after rotation is slightly off the original curve and the `curve.IPE(node.point)` returns False! For now the `object.contians(point)` check is disabled.
- [ ] Rays (and segments), eventhough different, they are rejected as similar they belong to the same line.
- [ ] It is important to reject invalide intersection points (e.g. duplicated points). In case of `Arrangement._multiProcessing` is True, the `distance_star()` is updated to reject invalid intersections resulting from arcs. But the counter part in not multi-processing should be updated to reject those wrong intersections.
- [ ] How to define the center of a face?
--->

License
-------
Distributed with a BSD license; see LICENSE.
```
Copyright (C) Saeed Gholami Shahbandi <saeed.gh.sh@gmail.com>
```

