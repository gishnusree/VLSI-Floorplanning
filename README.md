#  VLSI Physical Design: Floorplanning, Pin Assignment, and Power/Ground Routing

In VLSI physical design, floorplanning is the second step after partitioning. While partitioning divides a design into individual modules, floorplanning determines the shape, relative location, and pin positions of each block to optimize the chip layout. It handles hard blocks (fixed area and dimensions, e.g., PLLs, ADCs, or memory IPs) and soft blocks (fixed area but flexible aspect ratio). Unlike placement, which assigns exact coordinates, floorplanning only defines relative positions. Inputs include the set of blocks, their areas, possible orientations, number of pins, and the netlist. The output of floorplanning feeds into placement. The objectives are to optimize block locations and shapes, assign pin locations to minimize interconnect distance, reduce total wirelength, minimize the global bounding box area, and consider signal delay along critical paths using static timing analysis (STA). A combined objective can be expressed as:

$$
\text{Objective} = \alpha \cdot \text{Area} + (1 - \alpha) \cdot \text{Wirelength}, \quad 0 \le \alpha \le 1
$$

Floorplanning techniques include slicing floor plans, represented by a slicing tree with blocks as leaves and cuts as internal nodes, and non-slicing floor plans that allow flexible block arrangements. Representations such as constraint graphs (horizontal HCG and vertical VCG) and sequence pairs systematically encode relative positions. In HCG, an edge from block $M_i$ to $M_j$ exists if $M_i$ is left of $M_j$; in VCG, if $M_i$ is below $M_j$. Sequence pairs use two sequences $S^+$ and $S^-$ to encode block positions: if $A$ appears before $B$ in both sequences, $A$ is left of $B$; if $A$ appears before $B$ in $S^+$ and after $B$ in $S^-$, $A$ is above $B$. Coordinates are derived using the Longest Common Subsequence (LCS) algorithm, considering block widths, heights, and packing directions. The global bounding box dimensions are then:

$$
\text{Width} = \max(X_i + W_i), \quad \text{Height} = \max(Y_i + H_i)
$$

Shape functions define feasible width ($w$) and height ($h$) combinations for each block while keeping area constant ($A = w \times h$), respecting technology constraints and IP restrictions. Vertical composition sums heights and takes the maximum width:

$$
W_\text{floor} = \max(W_i), \quad H_\text{floor} = \sum H_i
$$

Horizontal composition sums widths and takes the maximum height:

$$
W_\text{floor} = \sum W_i, \quad H_\text{floor} = \max(H_i)
$$

The floorplan area is then:

$$
A_\text{floor} = W_\text{floor} \times H_\text{floor}
$$

Common floorplanning algorithms include Linear Ordering, which generates an initial block sequence minimizing wirelength using gains computed as:

$$
\text{Gain} = \text{terminating nets} - \text{new nets}
$$

Cluster Growth iteratively adds blocks to a growing cluster to minimize the bounding box area. Simulated Annealing is a probabilistic optimization that accepts worse solutions with probability

$$
P(\text{accept}) = e^{-\Delta C / T}
$$

to escape local minima.

After floorplanning, pin assignment connects all nets to unique pin locations to optimize performance. External pins connect I/O signals, minimizing interconnect length and parasitics, while internal pins reduce congestion and wirelength within modules. Methods include the concentric circle method, which projects block pins and chip pins onto circles to iteratively minimize Euclidean distances:

$$
d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}
$$

and topological pin assignment, which considers external block positions and multipin nets.

Power and ground routing ensures reliable chip operation by designing a robust power distribution network (PDN) that minimizes IR drop, balances power density, and supports voltage/current fluctuations. Inputs include total chip power, maximum power density, and dynamic power variations. Each block is connected to VDD and GND using thick metal lines and multiple vias. Approaches include planar routing for full-custom designs, where power lines follow a Hamiltonian path, and mesh routing for standard-cell designs, where metal grids and rings provide low-resistance connections. Key considerations include power density:

$$
\text{Power Density} = \frac{\text{Power in region}}{\text{Area of region}}
$$

metal width proportional to current requirement, and minimizing IR drop using wider metals and multiple vias.

In summary, effective VLSI floorplanning, pin assignment, and power/ground routing leverage algorithms and systematic representations to optimize chip area, wirelength, signal integrity, and power delivery, forming the foundation of efficient and high-performance IC design.

