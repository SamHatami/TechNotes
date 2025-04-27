---
title: "Matrix Methods of Joints"
description: Fundamentals of analyzing plane trusses using matrix methods
date: 2025-04-27
categories:
  - Computational Mechanics
tags:
  - Truss Analysis
math: true
comments: true
---

A direct translation of Method of Joints in matrix form is to solve all local joint equillibrium equations in "one" go. This is a key advantage when 
going for automation.

## Matrix formulation
The goal is to solve the matrix operation, which is for statically determined trusses.

$$
    x = A^{-1}b
$$
Where 
- *b* is a collection of our externally known forces
- *A* is a collection of known member directions
- *x*  is the collection of unknown forces and reactions

## Matrix Construction Requirements
To perform matrix operations, we need a square matrix. Each row corresponds to force equilibrium equations, with coefficients representing the directional contribution of each member at the joints. Most entries will be zero except where members connect to joints.

The global matrix dimensions are:

- Rows = Number of Nodes × 2 (representing force equilibrium in both x and y directions)
- Columns = Number of Members + Total Number of Supports

This directly relates to the condition for a statically determinate structure:
$$
    members+reactions=2*nodes
$$

If the truss is unstable matrix A will become rectangular and thus has no solution, i.e it indicates a Mechanism.
### Building the Coefficient Matrix
When constructing the global matrix, we only consider the joints and external loading. The coefficients are the directional components of each member, representing their orientation in 2D space.

For example:

- Member 1's normalized direction might be (1,0), representing a horizontal member
- Each row represents equilibrium at a specific node in a specific direction

Consider the example

ADD EXAMPLE PICS AND MATRICES

## Solving by code
Math.Net Numerics
```csharp
public static double[,] BuildGlobalMatrix(this TrussStructure structure, Dictionary<int, int> nodeIndexMap)
{
    var nrOfRows = structure.Nodes.Count * 2;
    var nrOfColumns = structure.Members.Count + structure.TotalReactionComponents();

    var matrix = new double[nrOfRows, nrOfColumns];

    if (nodeIndexMap == null) nodeIndexMap = structure.BuildNodeIndexMap();

    for (var i = 0; i < structure.Members.Count; i++)
    {
        var startNodeIndex = nodeIndexMap[structure.Members[i].Start.Id];
        var endNodeIndex = nodeIndexMap[structure.Members[i].End.Id];

        var member = structure.Members[i];

        matrix[2 * startNodeIndex, i] = member.Direction.X;
        matrix[2 * startNodeIndex + 1, i] = member.Direction.Y;
        matrix[2 * endNodeIndex, i] = -member.Direction.X;
        matrix[2 * endNodeIndex + 1, i] = -member.Direction.Y;
    }

    var reactionIndex = structure.Members.Count;

    foreach (var support in structure.Supports)
    {
        var supportNodeIndex = nodeIndexMap[support.NodeData.Id];

        //The enum here is redundant, but kept for clarity.
        //user should be able to set the degree of freedom for each support and the global matrix should be built accordingly.
        //Pre-defined support types are just a convenience.
        //user will be able to set DoF for each support in the future in the GUI
        if (support.BoundaryCondition.Type == BoundaryConditionType.Pinned) 
        {
            matrix[2 * supportNodeIndex, reactionIndex] = 1;
            matrix[2 * supportNodeIndex + 1, reactionIndex + 1] = 1;
            reactionIndex += 2;
        }
        else if (support.BoundaryCondition.Type ==
                 BoundaryConditionType.Roller) //Should be allowed to set x or y to be free, or angled?
        {
            //TODO: Improve next pass.
            if (support.DegreeOfFreedom.Ux)
                matrix[2 * supportNodeIndex, reactionIndex] = 1;
            else if (support.DegreeOfFreedom.Uy)
                matrix[2 * supportNodeIndex + 1, reactionIndex] = 1;
            reactionIndex++;
        }
    }
    return matrix;
}
```

[Code on a simple 2D solver ](https://github.com/SamHatami/SamLabs.Structural/blob/main/SamLab.Structural/SamLab.Structural.Core/Analysis/TrussStructureSolver.cs)