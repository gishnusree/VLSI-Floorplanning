# VLSI Physical Design: Floorplanning, Pin Assignment, and Power/Ground Routing

Floorplanning is the second step in the VLSI physical design flow, following partitioning. While partitioning divides a design into individual modules, floorplanning determines the shape, relative location of each block, and pin positions to optimize the chip layout. It handles both hard blocks (fixed area and dimensions, e.g., PLLs, ADCs, or memory from a compiler) and soft blocks (fixed area but flexible aspect ratio). In contrast, placement is a more restricted stage where block shapes are fixed and exact coordinates are assigned, whereas floorplanning only provides relative positions. Inputs for floorplanning include the set of blocks, their areas, possible orientations, number of pins, and the netlist describing interconnections. Placement requires fewer inputs: predefined block shapes, pin locations, and netlist. The output of floorplanning feeds directly into the placement stage.

The objectives of floorplanning are to determine block locations and shapes for efficient gate placement, assign pin locations to minimize interconnect distance, minimize total wirelength to reduce propagation delay and power, and optimize the global bounding box, which is the minimum axis-aligned rectangle enclosing all blocks. Combined objective functions can be defined as 
Objective
=
𝛼
⋅
Area
+
(
1
−
𝛼
)
⋅
Wirelength
,
0
≤
𝛼
≤
1
Objective=α⋅Area+(1−α)⋅Wirelength,0≤α≤1, allowing prioritization between area and wirelength optimization. Signal delay along critical paths is also considered using static timing analysis (STA).

Floorplanning techniques include slicing floor plans, where the chip is recursively divided into horizontal and vertical rectangles, represented by a slicing tree with leaves as blocks and internal nodes as cuts. The slicing tree can be compactly represented using Polish expressions. Non-slicing floor plans allow more flexible block arrangements, such as wheels. Additional representations include constraint graphs (horizontal for width, vertical for height) and sequence pairs to encode relative block positions. In the horizontal constraint graph (HCG), an edge from block 
𝑀
𝑖
M
i
	​

 to 
𝑀
𝑗
M
j
	​

 exists if 
𝑀
𝑖
M
i
	​

 is left of 
𝑀
𝑗
M
j
	​

, with a source node placed left of all blocks and a sink node to the right. In the vertical constraint graph (VCG), an edge exists if 
𝑀
𝑖
M
i
	​

 is below 
𝑀
𝑗
M
j
	​

, with the source node below all blocks and the sink node above. Redundant edges are removed to simplify the graph.

A sequence pair uses two sequences 
𝑆
+
S
+
 and 
𝑆
−
S
−
 to represent a floorplan. Relative positions are encoded as: 
𝐴
A left of 
𝐵
B if 
𝐴
A appears before 
𝐵
B in both sequences, 
𝐴
A above 
𝐵
B if 
𝐴
A appears before 
𝐵
B in 
𝑆
+
S
+
 and after 
𝐵
B in 
𝑆
−
S
−
, 
𝐴
A below 
𝐵
B if 
𝐴
A appears after 
𝐵
B in 
𝑆
+
S
+
 and before 
𝐵
B in 
𝑆
−
S
−
, and 
𝐴
A right of 
𝐵
B if 
𝐴
A appears after 
𝐵
B in both sequences. The floorplan can be derived from a sequence pair given the origin, block widths and heights, and packing direction. The x and y coordinates of each block are computed using the Longest Common Subsequence (LCS) algorithm, which respects sequence pair constraints. For x-coordinates, the input is 
𝑆
+
S
+
, 
𝑆
−
S
−
, and block widths; for y-coordinates, 
𝑆
+
S
+
 (reversed), 
𝑆
−
S
−
, and block heights are used. For two blocks 
𝐴
A and 
𝐵
B with lower-left coordinates 
(
𝑋
𝑎
,
𝑌
𝑎
)
(X
a
	​

,Y
a
	​

) and 
(
𝑋
𝑏
,
𝑌
𝑏
)
(X
b
	​

,Y
b
	​

), and dimensions 
(
𝑊
𝑎
,
𝐻
𝑎
)
(W
a
	​

,H
a
	​

) and 
(
𝑊
𝑏
,
𝐻
𝑏
)
(W
b
	​

,H
b
	​

), 
𝐴
A is left of 
𝐵
B if 
𝑋
𝑎
+
𝑊
𝑎
≤
𝑋
𝑏
X
a
	​

+W
a
	​

≤X
b
	​

 and not above/below, and 
𝐴
A is above 
𝐵
B if 
𝑌
𝑎
≥
𝑌
𝑏
+
𝐻
𝑏
Y
a
	​

≥Y
b
	​

+H
b
	​

. After computing coordinates via LCS, the global bounding box is determined as 
Width
=
max
⁡
(
𝑋
𝑖
+
𝑊
𝑖
)
,
Height
=
max
⁡
(
𝑌
𝑖
+
𝐻
𝑖
)
Width=max(X
i
	​

+W
i
	​

),Height=max(Y
i
	​

+H
i
	​

), ensuring all positional constraints are satisfied.

Floorplanning algorithms aim to minimize floorplan area while optimizing block orientation and placement to satisfy constraints. The shape function defines feasible width-height combinations for each block while keeping area constant: 
𝐴
=
𝑤
×
ℎ
A=w×h. Multiple combinations can satisfy the area constraint, but only the non-dominated corner points along the boundary are candidates for optimization. The top-level floorplan shape function is determined using a bottom-up strategy, combining individual block shape functions through vertical composition (
𝑊
floor
=
max
⁡
(
𝑊
𝑖
)
,
𝐻
floor
=
∑
𝐻
𝑖
W
floor
	​

=max(W
i
	​

),H
floor
	​

=∑H
i
	​

) or horizontal composition (
𝑊
floor
=
∑
𝑊
𝑖
,
𝐻
floor
=
max
⁡
(
𝐻
𝑖
)
W
floor
	​

=∑W
i
	​

,H
floor
	​

=max(H
i
	​

)). The floorplan area is 
𝐴
floor
=
𝑊
floor
×
𝐻
floor
A
floor
	​

=W
floor
	​

×H
floor
	​

, and the combination with minimum area is selected. A top-down trace then determines the final dimensions and orientation of each block.

Common floorplanning algorithms include Linear Ordering, which generates an initial block sequence minimizing wirelength by computing gains (
Gain
=
terminating nets
−
new nets
Gain=terminating nets−new nets), Cluster Growth, which iteratively adds blocks to a growing cluster to minimize bounding box area, and Simulated Annealing, a stochastic optimization that can accept worse solutions with probability 
𝑃
(
accept
)
=
𝑒
−
Δ
𝐶
/
𝑇
P(accept)=e
−ΔC/T
 to escape local minima.

Following floorplanning, pin assignment connects all nets to unique pin locations, optimizing routability, wirelength, and signal integrity. External pins connect incoming and outgoing signals to I/O pins, reducing parasitics, while internal pins reduce congestion and wirelength within modules. Pins can be functionally equivalent (swappable without affecting functionality) or electrically equivalent (connected to the same net). Methods include the concentric circle approach, projecting pins onto circles and iteratively minimizing Euclidean distance 
𝑑
=
(
𝑥
2
−
𝑥
1
)
2
+
(
𝑦
2
−
𝑦
1
)
2
d=
(x
2
	​

−x
1
	​

)
2
+(y
2
	​

−y
1
	​

)
2
	​

, and topological pin assignment, which considers external block positions and multipin nets.

Finally, power and ground routing ensures reliable chip operation. The goal is to design a power distribution network (PDN) that minimizes IR drop, balances power density, and accommodates dynamic voltage/current variations. Inputs include total chip power, maximum power density, and block-specific current requirements. VDD and ground connections are routed using thick metal lines with multiple vias for low resistance. Approaches include planar routing for full-custom designs, using a Hamiltonian path to connect all blocks, and mesh routing for standard-cell designs, where rings and metal grids provide robust power delivery. Key considerations include power density (
Power Density
=
Power in region
/
Area of region
Power Density=Power in region/Area of region), metal width proportional to total current, and minimizing IR drop with wider metals and multiple vias.

In summary, VLSI floorplanning, pin assignment, and power/ground routing combine algorithmic strategies, geometric representations, and optimization techniques to minimize area, wirelength, and delay, while ensuring signal integrity, routability, and reliable power delivery. Proper understanding of constraint graphs, sequence pairs, shape functions, composition strategies, and optimization algorithms enables designers to systematically generate efficient and manufacturable layouts.
