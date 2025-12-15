---
tag: code_problem
time_elapsed: 65
difficulty: medium
category: stack
created: 2025-12-14T20:07
updated: 2025-12-14T20:27
---

# 853. Car Fleet

**Notes**

Was not able to get a fully working solution. Code below will pass the first two test cases but will fail during the the third test case when submitting on neetcode.

```python
class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        places = [{} for _ in range(target + 1)]
        num_fleets = 0
        num_cars_complete = 0
        print(places)
        print("START")
        # Setup initial placing
        for i, pos in enumerate(position):
            places[pos][f"car-{i}"] = (pos, speed[i])

        # Keep looping till all cars are done
        while num_cars_complete < len(position):
            for j in range(target, -1, -1):
                # Skip looping till we find a car
                if not places[j]:
                    continue

                cars = places[j]
                cars_moved = []
                for car_name, car_details in cars.items():
                    curr_pos, speed = car_details
                    k = curr_pos + 1
                    print(curr_pos, speed)
                    while k < target and k < int(curr_pos) + int(speed):
                        # If there are no cars then let the car move forward
                        if not places[k]:
                            k += 1
                            continue

                        # If a car is found then slow down 
                        speed = min([val[1] for val in places[k]])
                    places[k][car_name] = (k, speed)
                    cars_moved.append(car_name) 

                for c in cars_moved:
                    del cars[car_name]
                
            if places[-1]:
                # Cars have finished so remove and update counters
                print("FOUND", places)
                num_fleets += 1
                num_cars_complete += len(places[-1])
                places[-1] = {}
                print(places)
        
        return num_fleets
```

**Update (2025-12-14T20:16)**

The following code below will now pass 10/22 test cases (better but still obviously not optimal)

```python
class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        places = [{} for _ in range(target + 1)]
        num_fleets = 0
        num_cars_complete = 0
        # Setup initial placing
        for i, pos in enumerate(position):
            places[pos][f"car-{i}"] = (pos, speed[i])

        # Keep looping till all cars are done
        while num_cars_complete < len(position):
            for j in range(target, -1, -1):
                # Skip looping till we find a car
                if not places[j]:
                    continue

                cars = places[j]
                cars_moved = []
                for car_name, car_details in cars.items():
                    curr_pos, speed = car_details
                    k = curr_pos + 1
                    while k < target and k < int(curr_pos) + int(speed):
                        # If there are no cars then let the car move forward
                        if not places[k]:
                            k += 1
                            continue

                        # If a car is found then slow down 
                        speed = min([val[1] for val in places[k].values()])
                    places[k][car_name] = (k, speed)
                    cars_moved.append(car_name) 
                for c in cars_moved:
                    del cars[c]
                
            if places[-1]:
                # Cars have finished so remove and update counters
                num_fleets += 1
                num_cars_complete += len(places[-1])
                places[-1] = {}
        
        return num_fleets
```

**Update (2025-12-14T20:27)**

***Notes***

After taking a look at solutions the point should be not to continually look forward from the first car found, but instead to use the furthest car and compare with the next closest and see if the time to `target` causes a collision.

Think of it as a system of linear equations (with the speed being the slope). Eventually the cars will cross if the combine to become a car fleet.

The code below provides a solution that will pass all test cases.

```python
class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        pairs = [(p, s) for p, s in zip(position, speed)]

        stack = []
        for p, s in sorted(pairs)[::-1]:
            stack.append((target - p) / s)
            if len(stack) > 1 and stack[-1] <= stack[-2]:
                stack.pop()
        return len(stack)
```

***Complexity Analysis***

We are only iterating over the sorted array once, thus giving us an `O(n)` time complexity. However, the array is being sorted. This therefore brings the time complexity to `O(nlogn)`.

Space complexity is `O(n)` as we are using a `pairs` array and a `stack` that can hold up to `n` elements each.