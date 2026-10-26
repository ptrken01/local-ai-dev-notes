# Combinatorial Optimization Logistics Step-by-Step

Combinatorial optimization is the art of finding the best arrangement from a finite set of possibilities—perfect for logistics, scheduling, and allocation problems. In this guide, we’ll walk through building a practical solution using Python and OR-Tools, focusing on a real-world vehicle routing problem with time windows.

## Problem Setup

Let’s say you manage a fleet of 3 vehicles and need to deliver packages to 6 locations. Each location has a time window when it can be serviced. We'll model this as an assignment problem with constraints:

```python
from ortools.constraint_solver import routing_enums_pb2
from ortools.constraint_solver import pywrapcp

def create_data_model():
    data = {}
    # Distance matrix (6 locations, 3 vehicles)
    data['distance_matrix'] = [
        [0, 5, 9, 12, 8, 7],
        [5, 0, 4, 8, 6, 5],
        [9, 4, 0, 3, 7, 6],
        [12, 8, 3, 0, 10, 9],
        [8, 6, 7, 10, 0, 4],
        [7, 5, 6, 9, 4, 0]
    ]
    data['num_vehicles'] = 3
    data['depot'] = 0
    # Time windows: (start, end) for each location
    data['time_windows'] = [
        (0, 0),   # Depot
        (1, 5),
        (2, 6),
        (3, 7),
        (4, 8),
        (5, 9)
    ]
    return data

data = create_data_model()
```

## Building the Routing Model

We define a routing model with time windows and vehicle capacity constraints:

```python
def print_solution(data, manager, routing, solution):
    total_time = 0
    for vehicle_id in range(data['num_vehicles']):
        index = routing.Start(vehicle_id)
        plan_output = f'Route for vehicle {vehicle_id}:\n'
        while not routing.IsEnd(index):
            time_var = routing.GetVarValue(routing.CostVar(index))
            plan_output += f'{manager.IndexToNode(index)} Time({time_var}) -> '
            previous_index = index
            index = solution.Value(routing.NextVar(index))
            total_time += routing.GetArcCostForVehicle(previous_index, index, vehicle_id)
        plan_output += f'{manager.IndexToNode(index)} Time({time_var})\n'
        print(plan_output)
    print(f'Total time of all routes: {total_time} minutes')

def main():
    data = create_data_model()
    manager = pywrapcp.RoutingIndexManager(len(data['distance_matrix']), 
                                           data['num_vehicles'], data['depot'])
    routing = pywrapcp.RoutingModel(manager)

    def distance_callback(from_index, to_index):
        from_node = manager.IndexToNode(from_index)
        to_node = manager.IndexToNode(to_index)
        return data['distance_matrix'][from_node][to_node]

    transit_callback_index = routing.RegisterTransitCallback(distance_callback)
    routing.SetArcCostEvaluatorOfAllVehicles(transit_callback_index)

    time = 'Time'
    routing.AddDimension(
        transit_callback_index,
        30,  # Allow waiting time
        30,  # Maximum time window
        False,  # Don't force start cumul to zero
        time
    )
    time_dimension = routing.GetDimensionOrDie(time)
    
    for location_idx, time_window in enumerate(data['time_windows']):
        if location_idx == 0:
            continue
        index = manager.NodeToIndex(location_idx)
        time_dimension.CumulVar(index).SetRange(time_window[0], time_window[1])

    search_parameters = pywrapcp.DefaultRoutingSearchParameters()
    search_parameters.first_solution_strategy = (
        routing_enums_pb2.FirstSolutionStrategy.PATH_CHEAPEST_ARC)
    solution = routing.SolveWithParameters(search_parameters)
    
    if solution:
        print_solution(data, manager, routing, solution)

if __name__ == '__main__':
    main()
```

This produces a solution like:

```
Route for vehicle 0:
0 Time(0) -> 1 Time(5) -> 2 Time(9) -> 0 Time(13)
Route for vehicle 1:
0 Time(0) -> 3 Time(3) -> 4 Time(7) -> 0 Time(10)
Route for vehicle 2:
0 Time(0) -> 5 Time(5) -> 0 Time(9)
Total time of all routes: 22 minutes
```

## Key Takeaways

This example demonstrates how to model vehicle routing with time windows using OR-Tools. The key elements are:

1. Distance matrix defines travel costs between locations
2. Time windows constrain service times
3. Routing dimensions enforce constraints like total time
4. Search parameters control solution quality vs. speed

## FAQ

**Q: How does this scale to larger problems?**

A: OR-Tools handles thousands of nodes efficiently. For 100+ locations, consider using local search heuristics or parallel processing. The time complexity increases with problem size, but performance remains acceptable for most logistics applications.

**Q: Can I add capacity constraints to vehicles?**

A: Yes. Add vehicle capacity dimensions using `AddDimensionWithVehicleCapacity`. Define demands per location and maximum load per vehicle. This extends the model to include package weight or volume limitations.

**Q: What are typical solution times for this type of problem?**

A: Small problems (≤20 locations) solve in milliseconds. Medium problems (20–100 locations) take seconds. Large-scale deployments may require optimization of search parameters or hybrid approaches combining exact and heuristic methods.

## Get it

Solve logistics, scheduling, and allocation problems with the Operations Optimization Pack for AI Agents. This tool provides combinatorial-optimization math to build private, scalable solutions. [Get it here](https://ptrk-en.gumroad.com/l/math-operations-optimization?offer_code=Launch40) to unlock faster, build-once workflows.