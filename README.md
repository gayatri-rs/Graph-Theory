Graham Pollak Theorem

Imagine you have a tree, i.e. a connected graph with no cycles. For this tree, we can create a matrix where each entry shows the number of edges you need to travel to get from one vertex (point) to another. For example, in a simple 3-vertex tree, the distance between connected points is 1, and between the two end points is 2. Suppose it has n vertices. For such a tree, you can form a n×n distance matrix. The determinant of this matrix turns out to depend only on the number of vertices, not on the specific shape of the tree. And that fact is essentially what the Graham–Pollak theorem describes!

The Graham-Pollak theorem reveals a fascinating property about trees (a type of graph without any cycles) and their distance matrices. The Graham–Pollak theorem—originally from 1971—can be phrased like this:

If you label the vertices of a tree with numbers 1,2,…,n, and form the matrix D=[d(i,j)] where each entry d(i,j) is the distance (number of edges) between vertex i and vertex j, then the determinant of D depends only on n, not on how the tree looks. In fact, the determinant is: 
det(D)= (−1)(n−1)(n−1)2(n−2)

So for any tree with n vertices—whether it’s a path, a star, or something spiky—when you fill in the distance matrix, perform reduction operations and calculate the determinant, you’ll always get that same number.


(Take note that the indexing in matrix starts from 0 . Hence all the steps are performed accordingly.)

First let us define function to get the determinant of resulting distance matrix by Schur Complement
You are encouraged to run the code and see the outputs, or refer to the attached files.

    def schur_determinant(M):
    
        n = M.nrows()
    
    
    # Standard approach: A is (n-1)×(n-1), B is (n-1)×1, C is 1×(n-1), D is 1×1
        A = M[1:,1:]  
        print (f"let A be:\n{A}") # (n-1)×(n-1)
        C = M[1:, 0]  
        print (f"let C be:\n{C}")      # (n-1)×1
        B = M[0,1:]     # 1×(n-1)
        print (f"let B be:\n{B}")
        D = M[0, 0]      # 1×1 (scalar)
        print (f"let D be:\n{D}")
    
    # Schur complement: S = D - C*A^(-1)*B
       schur_comp = D - B * A.inverse() * C
       L= det(A) * det(schur_comp)
       print(f"schur_comp = D - B*A.inverse()* C")# This is a scalar
       print(f"D2= det(A) * det(schur_comp)")
       return round(L)
Let us define the process as a function
              
    def graham_pollak(n,edges):
    T = Graph(edges)
    print("The graph is given as:")
    T.show() 
    D = T.distance_matrix()
    print("The Distance matrix is:")
    print(D)
    for v in T.vertices():
        vertices_with_degree_1 = [v for v in T.vertices() if T.degree(v) == 1] 
        if vertices_with_degree_1:  # Check if the list is not empty
            vertex_to_delete = vertices_with_degree_1[-1]
            print(f" D[0,{vertex_to_delete-1}] < D[0,{T.neighbors(vertex_to_delete)[0]-1}]")
            if D[0, vertex_to_delete-1] > D[0, T.neighbors(vertex_to_delete)[0]-1]:
                print(f"Consider vertex pendant v{vertex_to_delete}. \nPerform the following operations:")
                print(f"C{vertex_to_delete} -> C{vertex_to_delete} - C{T.neighbors(vertex_to_delete)[0]}")
                print(f"R{vertex_to_delete}->R{vertex_to_delete} - R{T.neighbors(vertex_to_delete)[0]}")
                D[vertex_to_delete-1, :] = D.row(vertex_to_delete-1) - D.row(T.neighbors(vertex_to_delete)[0]-1)
                D[:, vertex_to_delete-1] = D.column(vertex_to_delete-1) - D.column(T.neighbors(vertex_to_delete)[0]-1)
                print(D)
                print(f"Now remove the vertex v{vertex_to_delete} from the graph. The tree will look like:")
                T.delete_vertex(vertex_to_delete)
                T.show()
           
        else:
            print("No vertices with degree 1 found.")  # Handle case with no degree 1 vertices

    print(f"Finally we have the Determinant of D2 (by Schur complement) : {schur_determinant(D)}")
    c = (-1)**(n-1) * (n-1) * 2**(n-2)
    print("The determinant from the formula: ", c) 

  Let us work on a Random tree ie using only the number of vertices 
Note that for random graphs indexing starts from 0 so we make lil changes accordingly.

    def graham_pollak_ver(n):
    T =graphs.RandomTree(n)
    print("The graph is given as:")
    T.show() 
    D = T.distance_matrix()
    print("The Distance matrix is:")
    print(D)
    for v in T.vertices():
        vertices_with_degree_1 = [v for v in T.vertices() if T.degree(v) == 1] 
        if vertices_with_degree_1:  # Check if the list is not empty
            vertex_to_delete = vertices_with_degree_1[-1]
            print(f" D[0,{vertex_to_delete}] < D[0,{T.neighbors(vertex_to_delete)[0]}]")
            if D[0, vertex_to_delete] > D[0, T.neighbors(vertex_to_delete)[0]]:
                print(f"Consider vertex pendant v{vertex_to_delete}. \nPerform the following operations:")
                print(f"C{vertex_to_delete} -> C{vertex_to_delete} - C{T.neighbors(vertex_to_delete)[0]}")
                print(f"R{vertex_to_delete} -> R{vertex_to_delete} - R{T.neighbors(vertex_to_delete)[0]}")
                D[vertex_to_delete, :] = D.row(vertex_to_delete) - D.row(T.neighbors(vertex_to_delete)[0])
                D[:, vertex_to_delete] = D.column(vertex_to_delete) - D.column(T.neighbors(vertex_to_delete)[0])
                print(D)
                print(f"Now remove the vertex v{vertex_to_delete} from the graph. The tree will look like:")
                T.delete_vertex(vertex_to_delete)
                T.show()
            
        else:
            print("No vertices with degree 1 found.")  # Handle case with no degree 1 vertices

    print(f"Finally we have the Determinant of D2 (by Schur complement) : {schur_determinant(D)}")
    c = (-1)**(n-1) * (n-1) * 2**(n-2)
    print("The determinant from the formula: ", c)

Why does this happens? (Intuition) 
A tree might look very different depending on how its edges connect, but all trees of the same size share a few universal properties:

There are exactly n−1 edges.

There’s a unique simple path between any two vertices (because no cycles).

The way distances “flow” across the tree has a very systematic pattern.

Now, if you cleverly express the distance matrix D in terms of simpler matrices that depend on edges, you find that those dependencies cancel out in such a way that only n, the number of nodes, matters — not which tree you used. The tree distance matrix is a sum of simple rank‑1 contributions from edges. Different trees with the same n have the same number of edges and the same algebraic relationships among those edge vectors. These relationships ensure that all the distance matrices are equivalent up to transformations that do not change their determinant. That’s what Graham and Pollak demonstrated elegantly — the matrix operations “average out” all the specific geometry of the tree, leaving only the universal number.
