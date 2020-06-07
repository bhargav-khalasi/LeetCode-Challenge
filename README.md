# LeetCode-Challenge June-7, 2020
## Problem Statement: Queue Reconstruction by Height
Suppose you have a random list of people standing in a queue. Each person is described by a pair of integers (h, k), where h is the height of the person and k is the number of people in front of this person who have a height greater than or equal to h. Write an algorithm to reconstruct the queue.

Sample Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Sample Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]

## Solution
Here "to reconstruct the queue" statement implies to arrange a person x such that the number of persons ahead of him/her having height greater than or equal to person x should match with the value at second index of person x.

A <b>Simple solution</b> is to try all the possible arrangement of the given input and the time complexity of the same will be [O(n!)](https://en.wikipedia.org/wiki/Time_complexity#Factorial_time), where n is the number of elemnts in the list. Obviously we can do better than this. 

Note: I will use the sample input and output while discussing the approach. Also from here on, I will indicate person as element having two values h and k.

Consider an empty list named as output of size n, here taking n = 6 as the size of the sample input. Now lets try to fill our empty list with a single element first from the sample input, so if you observe carefully, the position of element with shortest h and k is independent of all the other elements. In our case that element is [4,4], as we know that this element is shortest and its k value is 4, so we need to have 4 elements before it and being the shortest, we can directly put [4,4] at index 4(considering start index at 0) because all other element occupying the list from 0 to 3 will be greater than or equal to (h=4). Now lets consider the second shortest element [5,0], it can be put at first empty space in the list as its k is 0. 

### Important: Basically, Here we are filling the list based on k value of current element i.e by skipping the k empty spaces before putting the current element in the output list and while skipping indexs, we are ignoring the elements which are already filled because they are always less than the current element except in one case which is discussed below.

Our Output list so far = [[5,0],[],[],[],[4,4],[]]

Taking the 3rd shortest element [5,2], we cannot directly put it at index 3 (skipping 2 empty space (1 and 2) from start based on k value) because now we have already placed one element ([5,0]) having same h value 5. So here we just need to keep track of all elements having same h before which is 1 in our case and subtracting it from curreny k=2, we get 1 and thus we can put [5,2] at index 2 (skipping 1 empty space from start). And based on similar idea, we can fill the entire list.

#### Code in Java
```java
// function to reconstruct the Queue.
public int[][] reconstructQueue(int[][] people) {
    if(people.length==0)return new int[0][0];
    
    // Sorting the array based on h and then k, if h are same.
    Arrays.sort(people,new Comparator<int[]>(){
            public int compare(int p[], int q[])
            {
                if(p[0]==q[0])
                {
                    return p[1]-q[1];
                }
                return p[0]-q[0];
            }
        });
        
        // ArrayList for tracking the the empty space index and is used to jump at ith empty space.
        ArrayList<Integer> arr = new ArrayList<>();
        for(int i=0;i<people.length;i++)arr.add(i);
        
        // Output Array
        int ans[][] = new int[people.length][2];
        
        // height and index of current element
        int h = -1;
        int pos = -1;
        
        // Tracking the element with same h value as the current element.
        // As the array is sorted we need to track only the current h.
        int count = 0;
        
        // looping over the people array
        for(int i=0;i<people.length;i++)
        {
            pos = people[i][1];
            if(people[i][0]==h)
            {
                count++; 
                pos -= count; // decreasing the index value, if it is found before.
            }
            else count=0; // assign count to 0, if there is break in h value from previous ones. 
            h = people[i][0];
            int p = arr.get(pos); // Taking the pos <sup>th</sup> empty index. 
            
            // filling the empty index with current h and k.
            ans[p][0] = h;
            ans[p][1] = people[i][1];
            
            // As the pos <sup>th</sup> index is filled, it is removed from the ArrayList.
            arr.remove(pos);
        }
        return ans;
    }
 }
```
