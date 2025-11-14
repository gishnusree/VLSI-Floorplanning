# VLSI Physical Design: Floorplanning, Pin Assignment, and Power/Ground Routing
In VLSI physical design, floor planning is the second step after partitioning. While partitioning divides a design into individual modules, floor planning determines the shape, relative location, and pin positions of each block to optimize the chip layout. It handles hard blocks (fixed area and dimensions, e.g., PLLs, ADCs, or memory IPs) and soft blocks (fixed area but flexible aspect ratio). Unlike placement, which assigns exact coordinates, floor planning only defines relative positions. Inputs include the set of blocks, their areas, possible orientations, number of pins, and the netlist. The output of floor planning feeds into placement. The objectives are to optimize block locations and shapes, assign pin locations to minimize interconnect distance, reduce total wirelength, minimize the global bounding box area, and consider signal delay along critical paths using static timing analysis (STA). A combined objective can be expressed as:

text
Objective = 𝛼 ⋅ Area + (1 − 𝛼) ⋅ Wirelength, 0 ≤ 𝛼 ≤ 1
Floor planning techniques include slicing floor plans, represented by a slicing tree with blocks as leaves and cuts as internal nodes, and non-slicing floor plans that allow flexible block arrangements. Representations such as constraint graphs (horizontal HCG and vertical VCG) and sequence pairs systematically encode relative positions. In HCG, an edge from block 𝑀𝑖 to 𝑀𝑗 exists if 𝑀𝑖 is left of 𝑀𝑗; in VCG, if 𝑀𝑖 is below 𝑀𝑗. Sequence pairs use two sequences 𝑆+ and 𝑆− to encode block positions: if 𝐴 appears before 𝐵 in both sequences, 𝐴 is left of 𝐵; if 𝐴 appears before 𝐵 in 𝑆+ and after 𝐵 in 𝑆−, 𝐴 is above 𝐵. Coordinates are derived using the Longest Common Subsequence (LCS) algorithm, considering block widths, heights, and packing directions. The global bounding box dimensions are then:

text
Width = max(𝑋𝑖 + 𝑊𝑖), Height = max(𝑌𝑖 + 𝐻𝑖)
Shape functions define feasible width (𝑤) and height (ℎ) combinations for each block while keeping area constant (𝐴 = 𝑤 × ℎ), respecting technology constraints and IP restrictions. Vertical composition sums heights and takes the maximum width:

text
𝑊_floor = max(𝑊𝑖), 𝐻_floor = ∑𝐻𝑖
Horizontal composition sums widths and takes the maximum height:

text
𝑊_floor = ∑𝑊𝑖, 𝐻_floor = max(𝐻𝑖)
The floor plan area is then 𝐴_floor = 𝑊_floor × 𝐻_floor.

Common floor planning algorithms include: Linear Ordering, which generates an initial block sequence minimizing wire length using gains computed as Gain = terminating nets − new nets; Cluster Growth, which iteratively adds blocks to a growing cluster to minimize the bounding box area; and Simulated Annealing, a probabilistic optimization that accepts worse solutions with probability 𝑃(accept) = 𝑒−Δ𝐶/𝑇 to escape local minima.

After floorplanning, pin assignment connects all nets to unique pin locations to optimize performance. External pins connect I/O signals, minimizing interconnect length and parasitics, while internal pins reduce congestion and wire length within modules. Methods include the concentric circle method, which projects block pins and chip pins onto circles to iteratively minimize Euclidean distances (𝑑 = √[(𝑥₂ − 𝑥₁)² + (𝑦₂ − 𝑦₁)²]), and topological pin assignment, which considers external block positions and multipin nets.

Power and ground routing ensures reliable chip operation by designing a robust power distribution network (PDN) that minimizes IR drop, balances power density, and supports voltage/current fluctuations. Inputs include total chip power, maximum power density, and dynamic power variations. Each block is connected to VDD and GND using thick metal lines and multiple vias. Approaches include planar routing for full-custom designs, where power lines follow a Hamiltonian path, and mesh routing for standard-cell designs, where metal grids and rings provide low-resistance connections. Key considerations include power density (Power Density = Power in region / Area of region), metal width proportional to current requirement, and minimizing IR drop using wider metals and multiple vias.

In summary, effective VLSI floorplanning, pin assignment, and power/ground routing leverage algorithms and systematic representations to optimize chip area, wirelength, signal integrity, and power delivery, forming the foundation of efficient and high-performance IC design.
