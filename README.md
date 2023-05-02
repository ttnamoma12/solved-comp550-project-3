Download Link: https://assignmentchef.com/product/solved-comp550-project-3
<br>
<strong>Algorithmic Robotics</strong>

<h1> Barking up a Random Tree</h1>

<strong>The documentation for </strong><strong>OMPL </strong><strong>can be found at </strong><a href="http://ompl.kavrakilab.org/"><strong>http://ompl.kavrakilab.org.</strong></a>

A broad class of motion planning algorithms consider only geometric constraints (e.g., bug algorithms, visibility graph methods, PRM). These algorithms compute paths that check only whether the robot is in collision with itself or its environment. In the motion planning literature, this problem is also known as <em>rigid body </em>motion planning or the <em>piano mover’s </em>problem. Physical constraints on the robot (e.g., velocity and acceleration limits, steering speed) are ignored in this formulation. While considering only geometric constraints may seem simplistic, many manipulators can safely ignore dynamical effects during planning due to their relatively low momentum or high torque motors. Formally, this is known as a <em>quasistatic </em>assumption.

The goal of this project is to implement a simple sampling-based motion planner in OMPL to plan for a rigid body and then systematically compare your planner to existing methods in the library.

<h2>Random Tree Planner</h2>

The algorithm that you will be implementing is the <em>Random Tree Planner</em>, or RTP. The RTP algorithm is a simple sampling-based method that grows a tree in the configuration space of the robot. RTP is loosely based on a random walk of the configuration space and operates as follows:

<ol>

 <li>Select a random configuration <em>q<sub>a </sub></em>from the existing Random Tree.</li>

 <li>Sample a random configuration <em>q<sub>b </sub></em>from the configuration space. With a small probability, select the goal configuration as <em>q<sub>b </sub></em>instead of a random one.</li>

 <li>Check whether the straight-line path between <em>q<sub>a </sub></em>and <em>q<sub>b </sub></em>in the C-space is valid (i.e., collision free). If the path is valid, add the path from <em>q<sub>a </sub></em>to <em>q<sub>b </sub></em>to the tree.</li>

 <li>Repeat steps 1 – 3 until the goal state is added to the tree. Extract the final motion plan from the tree.</li>

</ol>

The tree is initialized with the starting configuration of the robot. You might notice that this procedure seems very similar to other sampling-based algorithms that have been presented in class and in the reading. Many sampling-based algorithms employ a similar core loop that utilizes the basic primitives of selection, sampling, and local planning (checking). RTP is one of the simplest possible approaches, with no additional intelligence in how configurations are sampled, selected, or checked. Improvements to this algorithm are out of scope for this project, simply implement RTP as described above.

<h2>Benchmarking Motion Planners</h2>

Since many of the state-of-the-art algorithms for motion planning are built upon the concept of random sampling (including RTP), one run is not sufficient to draw statistically meaningful conclusions about the performance of your planner or any others. To help with evaluation, OMPL provides benchmarking functionalities that execute one or more planners many times while recording performance metrics in a database.

The ompl::tools::Benchmark class operates in two phases. First is the planning phase, where all of the planners are executed on the same problem for the given number of runs. Second is the analysis phase,

where the log file emitted by the benchmark class is post-processed into a SQLite database, and statistics for many common metrics are plotted to a PDF using ompl benchmark statistics.py, or using the online analysis available through <a href="http://plannerarena.org/">http://plannerarena.org/</a><a href="http://plannerarena.org/">.</a> The benchmark facilities are extensively documented at <a href="http://ompl.kavrakilab.org/benchmark.html#benchmark_code">http://ompl.kavrakilab.org/benchmark.html#benchmark</a> <a href="http://ompl.kavrakilab.org/benchmark.html#benchmark_code">code</a><a href="http://ompl.kavrakilab.org/benchmark.html#benchmark_code">.</a>

Note: ompl benchmark statistics.py requires matplotlib v1.2+ for Python, which can be installed through your favorite package manager or through Python’s pip program. The virtual machine should already have this installed. The script will produce box plots for continuously-valued performance metrics. If you are unfamiliar with these plots, <a href="https://en.wikipedia.org/wiki/Box_plots">Wikipedia</a> provides a good reference. The script will merge with any existing SQLite database with the same name, so take care to remove any previously existing database files before running the script. You can also upload your SQLite database to <a href="http://plannerarena.org/">http://plannerarena.org/</a> to interactively view your benchmark data.

<h2>Project exercises</h2>

<ol>

 <li>Implement RTP for rigid body motion planning. At a minimum, your planner must derive from ompl::base::Planner and correctly implement the solve(), clear(), and getPlannerData() functions. Solve should emit an exact solution path when one is found. If time expires, solve should also emit an approximate path that ends at the closest state to the goal in the tree.

  <ul>

   <li>Your planner does not need to know the geometry of the robot or the environment, or the exact C-space it is planning in. These concepts are abstracted away in OMPL so that planners can be implemented generically.</li>

  </ul></li>

 <li>Compute valid motion plans for a <em>point robot </em>within the plane and a <em>square robot </em>with known side length that translates and rotates in the plane using OMPL. Note, instead of manually constructing the state space for the square robot, OMPL provides a default implementation of the configuration space R<sup>2</sup>×S<sup>1</sup>, called ompl::base::SE2StateSpace.

  <ul>

   <li>Develop at least two interesting environments for your robot to move in. Bounded environments with axis-aligned rectangular obstacles are sufficient.</li>

   <li>Collision checking must be exact, and the robot should not leave the bounds of your environment. Inexact collision checking will lead to invalid motion plans. Verify that states and edges are both collision-free and that states do not leave the bounds of your environment.</li>

   <li>Visualize the world and the path that the robot takes to ensure that your planner and collision checker are implemented correctly. You must include visualizations of your worlds as well as some example paths your planner generated in your report.</li>

  </ul></li>

 <li>Benchmark your implementation of RTP in the 3D <em>Cubicles </em>and <em>Twistycool </em>scenarios from OMPL.app. Make sure to compare your planner against the PRM, EST, and RRT planners over at least 50 independent runs. If all of the planners fail to find a solution, you will need to increase the computation time allowed. See the appropriate OMPL.app .cfg files for the mesh names, start and goal configurations, etc. Use the results from your benchmarking to back up claims in your report about RTP’s performance quantitatively.

  <ul>

   <li>The ${OMPL DIR}/share/ompl/demos/SE3RigidBodyPlanning.cpp demo shows a code example of how to setup a problem using triangle meshes and benchmark planners. Feel free to use this as a reference for the benchmark exercise. The ompl::app::SE3RigidBodyPlanning class derives from ompl::geometric::SimpleSetup.</li>

  </ul></li>

</ol>

<h2>Protips</h2>

<ul>

 <li>It may be helpful to start from an existing planner, like RRT, rather than implementing RTP from scratch.</li>

</ul>

Check under the directory omplapp/ompl/src/ompl/geometric/planners/rrt if you compiled from source (or the virtual machine). The files are also found at <a href="https://bitbucket.org/ompl/ompl/src">https://bitbucket.org/ompl/ompl/src</a><a href="https://bitbucket.org/ompl/ompl/src">, </a>and on the online documentation available at <a href="http://ompl.kavrakilab.org/">http://ompl.kavrakilab.org/</a><a href="http://ompl.kavrakilab.org/">.</a>

Make sure you understand what each line of the RRT algorithm is doing. RRT is a much smarter algorithm than RTP, and while a good starting point, requires you to remove what is not required. You

will be penalized if code that is not a part of the RTP algorithm remains in your implementation. Additionally, although RRT has a variant that saves intermediate states, you should not implement a variant of RTP that saves intermediate states.

<ul>

 <li>The getPlannerData function is implemented for all planners in OMPL. This method returns a PlannerData object that contains the entire data structure (tree/graph) generated by the planner. getPlannerData is very useful for visualizing and debugging your tree.</li>

 <li>If your clear() function implemented in RTP is incorrect, you might run into problems when benchmarking as your planner’s internal data structures are not being refreshed.</li>

 <li>You do not need a nearest neighbor data structure for RTP. RTP has no need for neighbor proximity queries, as it selects states to extend from at random.</li>

 <li>RTP is not an intelligent planner, and is a very simple algorithm compared to other sampling-based planners. Keep this in mind when observing its performance.</li>

 <li>For benchmarking, make sure you give the planners a reasonable amount of time to solve the problem. If the time limit is too low, then it is highly unlikely your planner will solve any instances, and the resulting benchmark will not be informative. Make sure you understand when the planner returns an exact solution (it has solved the problem) and when it has returned an approximate solution (it has not solved the problem, and has returned a guess).</li>

 <li>Solution paths can be easily visualized using the printAsMatrix function from the PathGeometric class. Use any software you want to visualize this path, but provide your script with your submission. See <a href="http://ompl.kavrakilab.org/pathVisualization.html">http://ompl.kavrakilab.org/pathVisualization.html</a> for more details.</li>

 <li>In exercise 3, make sure to link your program against libompl, libompl app, and libompl app base against the problem instances. The provided Makefile given in the OMPL installation handout and the virtual machine does this.</li>

</ul>

<h2>Deliverables</h2>




To submit your project, clean your build space with make clean, zip up the project directory into a file named Project3 &lt;your NetID&gt; &lt;partner’s NetID&gt;.zip, and submit to Canvas. Your code must compile within a modern Linux environment. If your code compiled on the virtual machine, then it will be fine. If you deem it necessary, also include a README with details on compiling and executing your code. In addition to the archive, submit a short report that summarizes your experience from this project. The report should be anywhere from 1 to 5 pages in length in PDF format, and contain the following information:

<ul>

 <li>(2.5 points) A succinct statement of the problem that you solved.</li>

 <li>(2.5 points) A short description of the robots (their geometry) and configuration spaces you tested in exercise 2.</li>

 <li>(5 points) Images of your environments and a description of the start-goal queries you tested in exercise 2.</li>

 <li>(5 points) Images of paths generated by RTP in your environments you tested in exercise 2.</li>

 <li>(5 points) Summarize your experience in implementing the planner and testing in exercise 2. How would you characterize the performance of your planner in these instances? What do the solution paths look like?</li>

 <li>(15 points) Compare and contrast the solutions of your RTP with the PRM, EST, and RRT planners from exercise 3. Elaborate on the performance of your RTP. Conclusions <em>must </em>be presented quantitatively from the benchmark data. Consider the following metrics: computation time, path length, and the number of states sampled (graph states).</li>

 <li>(5 points) Rate the difficulty of each exercise on a scale of 1–10 (1 being trivial, 10 being impossible). Give an estimate of how many hours you spent on each exercise, and detail what was the hardest part of the assignment. Additionally, for students who completed the project in pairs, describe your individual contribution to the project.</li>

</ul>

Take time to complete your write-up. It is important to proofread and iterate over your thoughts. Reports will be evaluated for not only the raw content, but also the quality and clarity of the presentation. When referencing benchmarking results you must include the referenced data as figures within your report.

Additionally, you will be graded upon your implementation. Your code must compile, run, and solve the problem correctly. Correctness of the implementation is paramount, but succinct, well-organized,

well-written, and well-documented code is also taken into consideration. Visualization is an important component of providing evidence that your code is functioning properly. The breakdown of the grading of the implementation is as follows:

<ul>

 <li>(30 points) A correct implementation of RTP that generates valid motion plans.</li>

 <li>(30 points) Benchmarking of RTP against other planners in all prescribed environments.</li>

</ul>

Those completing the project in pairs need only provide one submission.