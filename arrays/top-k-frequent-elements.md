# Top K Frequent Elements

Given `nums` and integer `k`, return the `k` most frequent elements.

## Approach 1: Brute Force
- Count frequencies.
- Repeatedly find current max-frequency element `k` times by scanning map.
- Time: `O(n + k*m)` where `m` is unique elements, Space: `O(m)`.

### Java
```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.put(x, freq.getOrDefault(x, 0) + 1);

        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            int bestKey = 0, bestFreq = -1;
            for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
                if (e.getValue() > bestFreq) {
                    bestFreq = e.getValue();
                    bestKey = e.getKey();
                }
            }
            ans[i] = bestKey;
            freq.remove(bestKey);
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        vector<int> ans;
        while (k--) {
            int bestKey = 0, bestFreq = -1;
            for (auto& p : freq) {
                if (p.second > bestFreq) {
                    bestFreq = p.second;
                    bestKey = p.first;
                }
            }
            ans.push_back(bestKey);
            freq.erase(bestKey);
        }
        return ans;
    }
};
```

## Approach 2: Better (Min Heap)
- Count frequencies.
- Keep min-heap of size `k` by frequency.
- Time: `O(n log k)`, Space: `O(m + k)`.

### Java
```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.put(x, freq.getOrDefault(x, 0) + 1);

        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            pq.offer(new int[]{e.getKey(), e.getValue()});
            if (pq.size() > k) pq.poll();
        }

        int[] ans = new int[k];
        for (int i = k - 1; i >= 0; i--) ans[i] = pq.poll()[0];
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
        for (auto& p : freq) {
            pq.push({p.second, p.first});
            if ((int)pq.size() > k) pq.pop();
        }

        vector<int> ans;
        while (!pq.empty()) {
            ans.push_back(pq.top().second);
            pq.pop();
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

## Approach 3: Optimal (Bucket Sort)
- Count frequencies.
- Put numbers into bucket indexed by frequency.
- Traverse buckets from high to low until `k` elements collected.
- Time: `O(n)`, Space: `O(n)`.

### Java
```java
import java.util.*;

class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) freq.put(x, freq.getOrDefault(x, 0) + 1);

        List<Integer>[] buckets = new List[nums.length + 1];
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            int f = e.getValue();
            if (buckets[f] == null) buckets[f] = new ArrayList<>();
            buckets[f].add(e.getKey());
        }

        int[] ans = new int[k];
        int idx = 0;
        for (int f = buckets.length - 1; f >= 0 && idx < k; f--) {
            if (buckets[f] == null) continue;
            for (int x : buckets[f]) {
                ans[idx++] = x;
                if (idx == k) break;
            }
        }
        return ans;
    }
}
```

### C++
```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;

        vector<vector<int>> buckets(nums.size() + 1);
        for (auto& p : freq) buckets[p.second].push_back(p.first);

        vector<int> ans;
        for (int f = (int)buckets.size() - 1; f >= 0 && (int)ans.size() < k; f--) {
            for (int x : buckets[f]) {
                ans.push_back(x);
                if ((int)ans.size() == k) break;
            }
        }
        return ans;
    }
};
```
