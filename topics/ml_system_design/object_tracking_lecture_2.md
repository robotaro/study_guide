# Lecture 2 : Multiple Object Tracking

link [https://www.youtube.com/watch?v=BR3Y5bAz5Dw&list=PLog3nOPCjKBnpklR-RHZGWXbzjBkKI7cI&index=6]

## 1. Challenges
* Multiple objects with the same appearance

## 2. Review on Online tracking (For Frame-by-Frame Tracking)
1. Tracking initialisation using a detector
2. Matching detections at t with detectors at t+1 using something like:
    * **Bipartite matching** : 
        * Define distance between distances (IoU, pixels distance, 3d distance, etc)
        * Solve the "assignment problem" using the **Hungarian Algorithm**
3. Repeat for every pair of frames
* Problems: 
    * Cannot recover form errors, if detection is missing from a frame, you need to end the trajectory.
    * All decisions are essentially locall
* Solutions:
    * Find minimum cost solution for ALL frames and ALL trajectories 

## 3. Graph-based MOT (For multiple frames, video)
* You can track the detections as nodes over frames, where you need to connect the nodes over the frames to solve all objects.
    * Notes = Detections
    * Edge = flow = trajectory
    * 1 unit of flow = 1 object (a pedestrian, for example) 
* This problem is called the **Minimum Cost Flow Problem**
    * You want the lowest cost for each trajectory
    * The cost is the sum of the similarity between each pair of nodes (one from frame t-1 and one from t) times the indicator function.
    \[\tau*= arg min \sum_{i,j}C(i,j)f(i,j)\]
    * The indication function, f(i,j), has either a value of 0, for an inactive edge, or 1 otherwise. Value of 1 means the two nodes represent the same object/pedestrian
    * Finally, a trajectory can start at any node and stop at any node in the graph. So to make this "trainable", you create a **source** and **sink** nodes connected to every node in the graph. Once trained, these will indicate which node in the network repreasent the beginnign and end of each of the trajectories. 

I STOPPED HERE BECAUSE IT GETS REALLY DEEP IN TO GRAPHS AND GRAPH nEURAL NETWORKS