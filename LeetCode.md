[TOC]
# 哈希
### [1.两数之和](https://leetcode.cn/problems/two-sum/)
题解：

- 题意分析：在一个数组里面寻找两个值满足 `a + b = target`
- 基本思路：定一移一思想
   - 固定 a 移动 b 满足到 `a = target - b`即可返回
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int n = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < n; ++i) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{i, map.get(target - nums[i])};
            }
            map.put(nums[i], i);
        }
        return new int[2];
    }
}
```

### [242.有效的字母异位词](https://leetcode.cn/problems/valid-anagram/)
题解：

- 题意分析：判断两个字符串中字母出现的次数是否相等。
- 思路：哈希表统计字母出现次数
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        int[] num = new int[26];
        char[] chars = s.toCharArray();
        char[] chart = t.toCharArray();
        for (char ch : chars) {
            num[ch - 'a']++;
        }
        for (char ch : chart) {
            num[ch - 'a']--;
        }
        for (int i = 0; i < 26; i++) {
            if (num[i] != 0) {
                return false;
            }
        }
        return true;
    }
}
```

### [953.验证外星语词典](https://leetcode.cn/problems/verifying-an-alien-dictionary/)
题解：

- 题意分析：判断给定的$words$中的单词是不是按照给定的字典序已排序好的。
- 排序中可能存在的情况：
   - 字母不同：字典序中在前的字母排序在前
   - 字母相同：判断下一个字母，如果没有下一个字母，短的在前
- 思路：哈希表
   - 使用一个哈希表保存字典序，索引就是字典值，数组值就是其序列号
   - 然后两两遍历数组根据排序情况判断即可
```java
class Solution {
    public boolean isAlienSorted(String[] words, String order) {
        int[] map = new int[26];
        int count = 0;
        for (char c : order.toCharArray()) {
            count++;
            map[c - 'a'] = count;
        }
        for (int i = 0; i < words.length - 1; i++) {
            String s1 = words[i];
            String s2 = words[i + 1];
            if (!check(s1, s2, map)) {
                return false;
            }
        }
        return true;
    }

    // 判断两个字符串是否符合字典序
    private boolean check(String s1, String s2, int[] map) {
        int index = 0;
        while (index < s1.length() && index < s2.length()) {
            // 字母不同，数组中越小越前
            if (map[s1.charAt(index) - 'a'] < map[s2.charAt(index) - 'a']) {
                return true;
            } else if (map[s1.charAt(index) - 'a'] > map[s2.charAt(index) - 'a']) {
                return false;
            } else {
                index++;
            }
        }
        // 字母相同，字符串长度越小越前
        if (s1.length() <= s2.length()) {
            return true;
        } else {
            return false;
        }
    }
}
```

### [49.字母异位词分组](https://leetcode.cn/problems/group-anagrams/)
题解：

- 题意分析：题目要求给原数组进行分组，分组的条件是一个组里面的字符串之间要满足字母异位词，第二步就是明确字母异位词组的要求：字母出现频率相同，但位置不同。
- 关键思路：
   - 排序——排序后的字母异位词是相同的；
   - 哈希表——使用 hashmap 来去做分组操作，key 就是排序后的字符串，value 就是符合条件的字符串，使用 List 集合存储就自然构成了一个组。
- 算法思想：
   - 遍历源字符串数组，遍历过程中，先排序后更新 value：
      - 存在 key 直接添加，不存在就进行分组（新建列表），最后返回 value 集合。
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // key : 排序后的字符串，value : 分成的字符串组
        Map<String, List<String>> map = new HashMap<>();
        for (String str : strs) {
            char[] temp = str.toCharArray();
            Arrays.sort(temp);
            String key = new String(temp);
            List<String> value = map.getOrDefault(key, new ArrayList<>());
            value.add(str);
            map.put(key, value);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```

### [128.最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)
题解:

- 题意分析：给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
   - 关键点：序列是数字连续，这限制了最后结果数组中的数据范围。
- 算法思想：从暴力枚举开始去优化时间复杂度
   - 暴力方法下，每次枚举以当前位置为起点的连续序列，每次结束后更新答案即可。
   - 优化点一：判断元素存在可以用哈希表完成。——此步时间复杂度为$O(n)$
   - 暴力方法的核心问题在于重复——已知 `a~b` 连续序列的情况下不需要 再从`a + 1`开始，因此真正需要判断的是当前位置是不是遍历过的起点——哈希表判断 `a-1`是否存在，如果不存在说明**第一次从该起点枚举**，如果存在即说明枚举过直接跳过。
- 算法流程：
   - 使用 set 作为数据结构（因为需要对数组出现的数字进行去重），先遍历数组去重并统计数据；
   - 然后开始根据数值关系进行枚举。
      - $a-1$的位置被枚举过，直接跳；
      - $a-1$的未被枚举过，开始从此位置遍历。
- 时间复杂度：$O(n)$
```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int ans = 0;
        Set<Integer> set = new HashSet<>();
        for (int x : nums) {
            set.add(x);
        }
        for (int x : nums) {
            // 如果 x - 1 未被枚举过, 起点就是 x
            if (!set.contains(x - 1)) {
                // 需要记录当前连续序列长度以用于更新结果
                int cur = 1;
                // 需要判断是否值连续, 记录下起点值
                int start = x;
                // 从新起点 x 开始记录连续子序列长度
                while (set.contains(start + 1)) {
                    start++;
                    cur++;
                }
                ans = Math.max(ans, cur);
            }
        }
        return ans;
    }
}
```

### [560.和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)
题解：

- 题意分析：统计并返回数组中和为 k 的连续子数组的**个数 **
   - 注意这里要求子数组的元素是连续的；同时符合条件的子数组是可能出现元素重复的。
- 思路：前缀和+哈希表，使用哈希表来保存所有出现过的前缀和
   - ![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1706516505079-fc1d8a36-53bc-4ae3-b2d6-90d5075e0f90.png#averageHue=%23f8f3ed&clientId=u942632f9-afec-4&from=paste&height=158&id=u5e793f84&originHeight=198&originWidth=573&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=6436&status=done&style=none&taskId=u83021daa-9056-4eed-b74b-b8b56ae2a4d&title=&width=458.4)
   - 如果能够知道 x 的情况，那么统计和为 k 的子数组不就是统计和 x 的子数组吗！
      - x 是前缀和，遍历时记录下即可。
      - x 又可以通过`presum - k`得到。
      - 因此最终统计的结果就是哈希表保存的 value
- 注意：哈希表预先要存入$(0,1)$
   - 这是为了防止漏掉一种情况数组为$nums[0]+\dots+nums[i]=k$，此时如果按照代码逻辑是不会增加 count 的
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int n = nums.length;
        // key: 前缀和; value: 和出现的次数
        Map<Integer, Integer> map = new HashMap<>();
        // 防止漏掉情况，前缀和为 0
        map.put(0, 1);
        int ans = 0;
        int presum = 0;
        for (int x : nums) {
            presum += x;
            int d = presum - k;
            if (map.containsKey(d)) {
                ans += map.get(d);
            }
            map.put(presum, map.getOrDefault(presum, 0) + 1);
        }
        return ans;
    }
}
```

- 思考：为什么不能用双指针/滑动窗口？
   - 数组元素存在负值，无法判断窗口是否需要移动，也不能预先排序。
- 同类型的题目：
   - [930. 和相同的二元子数组](https://leetcode.cn/problems/binary-subarrays-with-sum/)
   - [1248. 统计「优美子数组」](https://leetcode.cn/problems/count-number-of-nice-subarrays/)
   - [974. 和可被 K 整除的子数组](https://leetcode.cn/problems/subarray-sums-divisible-by-k/)
   - [523. 连续的子数组和](https://leetcode.cn/problems/continuous-subarray-sum/)
   - [525.连续数组](https://leetcode.cn/problems/contiguous-array/)

930. 和相同的二元子数组
题解：
- 题意分析：统计并返回有多少个和为 goal 的 非空 子数组。
- 与上题代码完全相同
```java
class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {
        int n = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int ans = 0;
        int preSum = 0;
        for (int x : nums) {
            preSum += x;
            int d = preSum - goal;
            if (map.containsKey(d)) {
                ans += map.get(d);
            }
            map.put(preSum, map.getOrDefault(preSum, 0) + 1);
        }
        return ans;
    }
}
```

- 更优的方法是滑动窗口
   - 思路：nums[i] 没有负权值意味着前缀和数组必然具有（非严格）单调递增特性。
   - 不难证明，在给定 t 的情况下，当我们右端点 r 往右移动时，满足条件的左端点 l 必然往右移动。
   - 实现上，我们可以使用两个左端点 l1和 l2，代表在给定右端点 r 的前提下满足要求的左端点集合，同时使用 s1 和 s2 分别代表两个端点到 r这一段的和。
```java
class Solution {
    public int numSubarraysWithSum(int[] nums, int goal) {
        int n = nums.length;
        int left1 = 0, left2 = 0, right = 0;
        int sum1 = 0, sum2 = 0;
        int ans = 0;
        while (right < n) {
            sum1 += nums[right];
            while (left1 <= right && sum1 > goal) {
                sum1 -= nums[left1];
                left1++;
            }
            sum2 += nums[right];
            while (left2 <= right && sum2 >= goal) {
                sum2 -= nums[left2];
                left2++;
            }
            ans += left2 - left1;
            right++;
        }
        return ans;
    }
}
```

1248. 统计「优美子数组」

题解：

- 与上题代码基本相同，关键是理解这里的前缀和记录的是什么 
   - 记录当前位置之前奇数出现的次数
- 更优的方法是滑动窗口或者使用数组代替map
- 上题不行的原因是记录的是数组值，规模大

974. 和可被 K 整除的子数组
题解：

- 基本思路类似，关键一步：presum能被整除，前一段也能被整除，那么需要的后一段也能被整除——>`presum1%k==presum2%k`
- 哈希表如何定义：整段和前一段的余数要求相同，那么哈希表直接存储余数即可：key表示余数；value表示能被整除的个数。
- 注意：`nums=[-1,2,9]`这种情况下会出现负数余数，会漏计算2%2的情况，需要求余数时进行处理，纠正为1
- 可以使用数组降低复杂度，数据规模是固定的。

523. 连续的子数组和
题解：

- 题意：给你一个整数数组 `nums` 和一个整数 `k`，如果 `nums` 有一个 好的子数组 返回 `true` ，否则返回 `false`：
   - 好的子数组的两个要求：大小要大于等于 2，子数组和要能被 k 整除。
- 与上题的差别就在于子数组的大小要大于等于2。怎么去判断？
- 在哈希表中通过value去保存子数组的最后一个元素的数组下标即可。因为presum保存的是前缀和，那么目标子数组的长度就是i-value
- 注意：`nums=[5,0,0,0]`时出现错误：注意退出循环使用的是 `continue`
```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        int n = nums.length;
        // key: 余数; value: 子数组的最后一个元素下标
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, - 1);
        boolean ans = false;
        int preSum = 0;
        for (int i = 0; i < n; i++) {
            preSum += nums[i];
            // 子数组元素总和为 k 的倍数
            int key = preSum % k;
            if (map.containsKey(key)) {
                // 子数组长度至少为 2
                if (i - map.get(key) >= 2) {
                    ans = true;
                }
                continue;
            }
            map.put(key, i);
        }
        return ans;
    }
}
```

525.连续数组
题解：

- 题意分析：找到含有相同数量的 0 和 1 的最长连续子数组，并返回该子数组的长度
- 思路：前缀和+哈希表
   - **将 0 视为-1 加入数组中**，那么就**将问题转换成了求数组和为 0 的最长连续子数组的长度**
```java
class Solution {
    public int findMaxLength(int[] nums) {
        int n = nums.length;
        if (n == 1) {
            return 0;
        }
        int[] prefix = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            prefix[i] = prefix[i - 1] + (nums[i - 1] == 0 ? -1 : 1);
        }
        // key: 前缀和; value: 对应数组中的最小下标
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 0);
        int ans = 0;
        for (int i = 1; i <= n; i++) {
            int temp = prefix[i];
            if (map.containsKey(temp)) {
                ans = Math.max(ans, i - map.get(temp));
            } else {
                map.put(temp, i);
            }
        }
        return ans;
    }
}
```

### [380.O(1) 时间插入、删除和获取随机元素](https://leetcode.cn/problems/insert-delete-getrandom-o1/)
题解：

- 题意分析：要求设计数据结构满足：插入、删除、随机获取元素的时间复杂度都是$O(1)$
- 使用哈希表作为基础数据结构，哈希表的 put, set, get 操作都是常数级时间复杂度
   - 插入和删除$O(1)$时间复杂度简单，哈希表就能做到，如何实现随机获取元素也是$O(1)$
   - 使用数组完成随机获取元素操作。
- 思路：一个元素加入和删除都在哈希表和数组中操作一次。
   - 首先要确定随机获取元素的范围，初始化的数组是最大可能存在数的个数，显然数组中可能存在零散的空位置，我们需要人为的控制$[0,index]$的元素就是当前数据结构中保存的元素.
   - 根据上述条件来进一步约束插入删除操作：
      - 插入操作：直接插入哈希表和数组中，同时移动索引
      - 删除操作：当删除一个元素后，为了维护前 index+1 元素，需要将索引位置的元素给补过来。
```java
class RandomizedSet {
    Random random = new Random();
    Map<Integer, Integer> map;
    int[] nums = new int[20001];
    int index;

    public RandomizedSet() {
        map = new HashMap();
        index = -1;
    }
    
    public boolean insert(int val) {
        if (map.containsKey(val)) {
            return false;
        }
        nums[++index] = val;
        map.put(val, index);
        return true;
    }
    
    public boolean remove(int val) {
        if (!map.containsKey(val)) {
            return false;
        }
        int x = map.remove(val);
        if (x != index) {
            map.put(nums[index], x);
        }
        nums[x] = nums[index--];
        return true;
    }
    
    public int getRandom() {
        return nums[random.nextInt(index + 1)];
    }
}
```

### [539.最小时间差](https://leetcode.cn/problems/minimum-time-difference/)
题解：

- 题意分析：给定一个 24 小时制（小时:分钟 "HH:MM"）的时间列表，找出列表中任意两个时间的最小时间差并以分钟数表示。
- 思路一：使用哈希表保存所有时间的出现情况。
   - 需要注意时间是环形的，也就是说$23:59$和$00:00/24:00$的差值都是 1
   - 统计时需要**额外统计一个扩展部分的区域**
   - 计算结果时需要注意：出现重复时间串的情况，也就说 map 中值大于 1，那么结果一定是 0；
- 算法流程：
   - 哈希表存储时间出现情况，大小是两倍 24x60+1，下标是时间，值是出现次数；
   - 遍历时间列表来更新哈希表，因时间环形，每次更新需要统计+1440 的位置；
   - 遍历哈希表来统计结果：
      - 值大于 1 则说明出现重复直接返回 0；
      - 双指针更新结果。
```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        int n = timePoints.size();
        // 剪枝: 出现重复时间直接返回 0
        if (n > 1440) {
            return 0;
        }
        int[] map = new int[1440 * 2 + 1];
        for (String time : timePoints) {
            String[] parts = time.split(":");
            int hour = Integer.parseInt(parts[0]);
            int minutes = Integer.parseInt(parts[1]) + hour * 60;
            map[minutes]++;
            // 时间环形, 额外进行统计
            map[minutes + 1440]++;
        }
        int ans = 1441;
        int left = -1;
        for (int i = 0; i <= 1440 * 2 && ans != 0; i++) {
            if (map[i] == 0) {
                continue;
            }
            // 出现重复时间串
            if (map[i] > 1) {
                ans = 0;
            } else if (left != -1) {
                ans = Math.min(ans, i - left);
            }
            left = i;
        }
        return ans;
    }
}
```

- 思路二：排序，直接用一个数组保存出现的时间情况，然后排序一下，再统计结果即可。
   - 同样要注意时间环形，需要额外统计，也就是说统计的时候不仅统计了当天的，还统计了下一天该时间点的
   - 时间复杂度不如上一种解法。
```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        int n = timePoints.size();
        int[] times = new int[n * 2];
        for (int i = 0, index = 0; i < n / 2; i++, index += 2) {
            String[] parts = timePoints.get(i).split(":");
            int hour = Integer.parseInt(parts[0]);
            int minutes = Integer.parseInt(parts[1]) + hour * 60;
            times[index] = minutes;
            times[index + 1] = times[index] + 1440;
        }
        Arrays.sort(times);
        int ans = times[1] - times[0];
        for (int i = 0; i < n - 1; i++) {
            ans = Math.min(ans, times[i + 1] - times[i]);
        }
        return ans;
    }
}
```

# 双指针
### [283.移动零](https://leetcode.cn/problems/move-zeroes/)
题解：

- 题意分析：将 数组中的 0 都集中到数组末尾位置
- 算法思想：双指针+覆盖思路—我们并不需要真正的将 0 移动，只需要不断将后面的元素覆盖前面的零即可。双指针的作用在于确定非元素的起止位置。
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int n = nums.length;
        int slow = 0;
        for (int fast = 0; fast < n; fast++) {
            if (nums[fast] != 0) {
                nums[slow++] = nums[fast];
            }
        }
        for (; slow < n; slow++) {
            nums[slow] = 0;
        }
    }
}
```

### [11.盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)
题解：

- 题意分析：目标是求数组元素的下标和值能够构成的最大面积，使用公式表示就是$长=j-i;宽=Math.min(arr[i],arr[j])$
- 启发：水桶面积的关键在于短的那边
- 算法思路：基本思路就是**双指针遍历然后不断更新结果即可**，关键讨论细节 ——如何移动指针方向：要想值变大，**矮的一边需要优先变动，因此哪边矮移动哪边**。
- 时间复杂度：$O(n)$
- 可以使用类似于三数之和的优化：使用一个变量保存最小高度。
```java
class Solution {
    public int maxArea(int[] height) {
        int n = height.length;
        int left = 0, right = n - 1;
        int ans = 0;
        int minHeight;
        while (left < right) {
            minHeight = Math.min(height[left], height[right]);
            ans = Math.max(ans, minHeight * (right - left));
            while (left < right && minHeight >= height[left]) {
                left++;
            }
            while (left < right && minHeight >= height[right]) {
                right--;
            }
        }
        return ans;
    }
}
```

### [15.三数之和](https://leetcode.cn/problems/3sum/)
题解：

- 题意分析：给你一个整数数组 nums ，判断是否存在三元组`[nums[i],nums[j],nums[k]]`满足`i != j`、`i != k`且`j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 0 且不重复的三元组。
   - 注意：答案中不可以包含重复的三元组
- 算法思想：
   - 数组有序时可以使用相向双指针确定一个二元组，本题要求三元组，只需要在二元组的基础上固定住另一个元素即可。
   - 固定 k 那么$target=-nums[k]$，在此基础上求$nums[left]+nums[right]=target$——降维成了两数之和 II
- 处理上的一些关键点： 
   - 固定k，注意题目要求答案中**不能出现重复的三元组**，因此需要进行去重判断
      - 对 k 去重：`if (k > 0 && nums[k] == nums[k-1])`
   - 然后后续的处理和两数之和 Ⅱ 相同，求二元组即可，需要注意在收集结果后进去下层循环之前需要注意$i, j$**同样需要进行去重处理**。
      - 对 i 和 j 去重：`for (++i; i < j && nums[i] == nums[i - 1]; i++);``for(--j; i < j && nums[j] == nums[j + 1]; j--);`
- 可以优化的方向：数组有序性的启发
   - k 后紧接的两个元素组成的三元组如果都大于0的话直接`break`；
   - k 与末尾两个元素组成的三元组都小于0的话就不需要继续处理下去，直接`continue`。
```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        // 1. 先排序以便于后面是否双指针
        Arrays.sort(nums);
        int n = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        // var ans = new ArrayList<List<Integer>>();  JDK-10 语法
        // 2. 定一移一思想:定 k 移二元组
        for (int k = 0; k < n - 2; k++) {
            // 去重操作
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            // 优化一:数组有序性的启发
            if (nums[k] + nums[k + 1] + nums[k + 2] > 0) {
                break;
            }
            // 优化二:数组有序性的启发
            if (nums[k] + nums[n - 1] + nums[n - 2] < 0 ) {
                continue;
            }
            // 3. 双指针来计算二元组
            int target = nums[k];
            int i = k + 1, j = n - 1;
            while (i < j) {
                int sum = nums[i] + nums[j] + target;
                if (sum > 0) {
                    j--;
                } else if (sum < 0) {
                    i++;
                } else {
                    // res.add(List.of(target, nums[i], nums[j])); // jdk-9 语法
                    res.add(Arrays.asList(target, nums[i], nums[j]));
                    // 去重操作
                    do {
                        i++;
                    } while (i < j && nums[i] == nums[i + 1]);
                    // 去重操作
                    do {
                        j--;
                    } while (i < j && nums[j] == nums[j + 1]); 
                }
            }
        }
        return res;
    }
}
```

- 同类型的题目：
   - [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)
   - [16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)
   - [18. 四数之和](https://leetcode-cn.com/problems/4sum/) 

167. 两数之和 II - 输入有序数组

题解：

- 利用数组有序的性质，使用左右指针，根据sum和target比较来缩小范围；
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int n = numbers.length;
        int left = 0, right = n - 1;
        while (true) {
            int sum = numbers[left] + numbers[right];
            if (sum > target) {
                right--;
            } else if (sum < target){
                left++;
            } else {
                return new int[]{left + 1, right + 1};
            }
        }
    }
}
```

16. 最接近的三数之和

题解：

- 题意分析：与三数之和的差别在于本题没有给定 target 值为 0，而是要求三元组的和最接近 target 就行。
- 显然，一个 diff 变量来保存差值。
- 基本流程和三数之和相同，需要注意更新结果的情况。
- 可以优化的方向：前两种优化与上题类似，注意还有一种重复的情况：如果当前i和前一个值相等说明此值开头的三元组算过，直接进行下层循环。
```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int n = nums.length;
        int minDiff = Integer.MAX_VALUE;
        int ans = 0;
        for (int i = 0; i < n - 2; i++) {
            int x = nums[i];
            // 优化三
            if (i > 0 && x == nums[i - 1]) {
                continue;
            }
            // 优化一:与三数之和类似
            int temp = x + nums[i + 1] + nums[i + 2];
            if (temp > target) {
                if (temp - target < minDiff) {
                    minDiff = temp - target;
                    ans = temp;
                }
                break;
            }
            // 优化二:与三数之和类似
            temp = x + nums[n - 1] + nums[n - 2];
            if (temp < target) {
                if (target - temp < minDiff) {
                    minDiff = target - temp;
                    ans = temp;
                }
                continue;
            }
            // 双指针
            int j = i + 1, k = n - 1;
            while (j < k) {
                int sum = x + nums[j] + nums[k];
                if (sum == target) {
                    return sum;
                } else if (sum > target) {
                    if (sum - target < minDiff) {
                        minDiff = sum - target;
                        ans = sum;
                    } 
                    k--;
                } else {
                    if (target - sum < minDiff) {
                        minDiff = target - sum;
                        ans = sum;
                    }
                    j++;
                }
            }
        }
        return ans;
    }
}
```

1.  四数之和

题解：

- 题意分析：从三数之和问题上升四数之和问题
- 算法思想：同样是定一移一，确定 a 后问题变为三数之和，确定 b 后问题变为双指针。
- 进一步优化的方式：与三数之和类似
```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        int n = nums.length;
        if (n < 4) {
            return ans;
        }
        for (int i = 0; i < n - 3; i++) {
            // 去重
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            long x = nums[i];
            for (int j = i + 1; j < n - 2; j++) {
                // 去重
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                long y = nums[j];
                int k = j + 1, l = n - 1;
                while (k < l) {
                    long sum2 = x + y + nums[k] + nums[l];
                    if (sum2 > target) {
                        l--;
                    } else if (sum2 < target) {
                        k++;
                    } else {
                        ans.add(Arrays.asList(nums[i], nums[j], nums[k], nums[l]));
                        for (++k; k < l && nums[k] == nums[k - 1]; k++)
                        ;
                        for (--l; k < l && nums[l] == nums[l + 1]; l--)
                        ;
                    }
                }
            }
        }
        return ans;
    }
}
```

### [42.接雨水](https://leetcode.cn/problems/trapping-rain-water/)
题解：

- 题意分析：给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
   - 每个能接雨水的地方是左右柱子差值不同的位置。
- 双指针法：关键思路是接到雨水的空间是前后缀（最大值）与当前高度的差值。因此**怎么求当前位置左右最大高度是关键**
- 算法思路：
   - 每个接雨水柱子的值左右高度最小值减去当前位置的高度即可。
   - 通过相向双指针遍历数组，每次根据左右高度情况来更新结果即可。
- 使用额外空间： 
   - 建立每个位置的最大前缀值数组和最大后缀值数组。
   - 然后重新遍历数组累计结果：`ans += Math.min(pre_max,suf_max)-height[i]`
- 不使用额外空间： 
   - 双指针遍历的过程中去更新前后缀最大值`preMax = Math.max(preMax, height[left])`。
   - 而结果同样是取小的一边来累计同时移动指针。
```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int[] prefix = new int[n];
        prefix[0] = height[0];
        int[] suffix = new int[n];
        suffix[n - 1] = height[n - 1];
        int ans = 0;
        for (int i = 1; i < n; i++) {
            prefix[i] = Math.max(prefix[i - 1], height[i]);
        }
        for (int i = n - 2; i >= 0; i--) {
            suffix[i] = Math.max(suffix[i + 1], height[i]);
        }
        for (int i = 0; i < n; i++) {
            ans += Math.min(prefix[i], suffix[i]) - height[i];
        }
        return ans;
    }

    // 空间优化版
    public int trap(int[] height) {
        int n = height.length;
        int preMax = 0, sufMax = 0;
        int left = 0, right = n - 1;
        int ans = 0;
        while (left < right) {
            preMax = Math.max(preMax, height[left]);
            sufMax = Math.max(sufMax, height[right]);
            // right 位置
            if (preMax > sufMax) {
                ans += sufMax - height[right--]; 
            } else {    // left 位置
                ans += preMax - height[left++];
            }
        }
        return ans;
    }
}
```

# 滑动窗口
### [209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)
题解：

- 题意分析：找出该数组中满足其总和大于等于 `target` 的长度最小的连续子数组 $[nums_l, nums_{l+1}, ..., nums_{r-1}, nums_r]$ ，并返回其长度。
- 思路：快慢指针
   - 快指针用于统计子数组的数组和
   - 慢指针用于确定子数组长度
- 注意这里**使用 while 循环来更新左边界**，因为每次加的右边的数，减的是左边的数
   - 先因为加了一个很大的数导致大于 target，但可能开始加入进去的数很小
   - 窗口大小不固定
```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        int ans = n + 1;
        int slow = 0;
        int sum = 0;
        for (int fast = 0; fast < n; fast++) {
            sum += nums[fast];
            while (sum >= target) {
                ans = Math.min(ans, fast - slow + 1);
                sum -= nums[slow++];
            }
        }
        return ans == n + 1 ? 0 : ans;
    }
}
```

- 拓展思路：前缀和+二分查找
- **_因为这道题保证了数组中每个元素都为正，所以前缀和一定是递增的，这一点保证了二分的正确性。如果题目没有说明数组中每个元素都为正，这里就不能使用二分来查找这个位置了。_**
- 由暴力解优化而来：本质上这题目标就是寻找一个二元组 [left,right], 在确定子数组起始下标后，使用二分查找查找 right 即可
   - 二分查找查找目标是找到$sum[right]-sum[left]\ge target$
   - 那就可以固定$left$根据$target+sum[left]$来查找$right$
```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        if (n == 0) {
            return 0;
        }
        int ans = Integer.MAX_VALUE;
        int[] sums = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            sums[i] = sums[i - 1] + nums[i - 1];
        }
        for (int i = 1; i <= n; i++) {
            int d = target + sums[i - 1];
            int bound  = Arrays.binarySearch(sums, d);
            if (bound < 0) {
                bound = -bound - 1;
            }
            if (bound <= n) {
                ans = Math.min(ans, bound - (i - 1));
            }
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }
}
```

- 同类型的题目：
   - [713.乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

713.乘积小于 K 的子数组

题解：

- 题意分析：与上题的差别在于：
   - 要求计算的是乘积；
   - 要求统计的是满足条件的子数组个数
- 思路：也是基于快慢指针完成，统计结果时是整个区间长度
```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k <= 1) {
            return 0;
        }
        int n = nums.length;
        int ans = 0;
        int prod = 1;
        int slow = 0;
        // 滑动窗口更新连续数组
        for (int fast = 0; fast < n; fast++) {
            prod *= nums[fast];
            while (prod >= k) {
                prod /= nums[slow++];
            }
            // 乘积刚小于 k 时统计结果
            ans += fast - slow + 1;
        }
        return ans;
    }
}
```

### [3.无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)
题解：

- 题意分析：子串两个要求：子串中没有字母重复；长度最大
- 基本思路：子串没有字母重复-哈希表；长度最大-滑动窗口边遍历边更新
- 算法流程：滑动窗口遍历数组，当出现重复则需要缩小左边界**（窗口的大小是不固定的，因此while 循环完成窗口更新）**
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 1. 定义哈希表以及初始化参数
        int len = s.length();
        if (len <= 1) {
            return len;
        }
        char[] ch = s.toCharArray();
        int ans = 0;
        boolean[] hash = new boolean[128];
        // 2. 滑动窗口更新结果
        int slow = 0;
        for (int fast = 0; fast < len; fast++) {
            while (hash[ch[fast]]) {
                hash[ch[slow++]] = false;
            }
            hash[ch[fast]] = true;
            ans = Math.max(ans, fast - slow + 1);
        }
        return ans;
    }
}
```

### [438.找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)
题解：

- 题意分析：首先明确异位词：字母相同即可（排列顺序相同不同都符合）——哈希表；寻找子串——滑动窗口
- 基本思路：先统计匹配子串的词频，然后滑动窗口遍历目标串，同时统计子串的词频并比较即可。
   - 这里不用 while 而是用 if 是因为滑动窗口的大小是固定的 `p.length()`
- 优化思路：比较方法时使用遍历对比两个数组，其中很多空位置带来没必要的时间复杂度，只需要优化成遍历过程中直接操作哈希表即可。
   - 统计下实际存在的字母个数；
```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        int m = s.length(), n = p.length();
        int[] countS = new int[26], countP = new int[26];
        for (char c : p.toCharArray()) {
            countP[c - 'a']++;
        }
        List<Integer> ans = new ArrayList<>();
        int slow = 0;
        char[] chs = s.toCharArray();
        for (int fast = 0; fast < m; fast++) {
            countS[chs[fast] - 'a']++;
            // 滑动窗口
            if (fast - slow + 1 > n) {
                countS[chs[slow++] - 'a']--;
            }
            // 收集结果
            if (isEqual(countP, countS)) {
                ans.add(slow);
            }
        }
        return ans;

    }

    private boolean isEqual(int[] c1, int[] c2) {
        for (int i = 0; i < 26; ++i) {
            if (c1[i] != c2[i]) {
                return false;
            }
        }
        return true;
    }
}

// 优化版(基本时间复杂度没变)
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        List<Integer> ans = new ArrayList<>();
        int n = s.length(), m = p.length();
        int[] cnt = new int[26];
        for (int i = 0; i < m; i++) {
            cnt[p.charAt(i) - 'a']++;
        }
        int a = 0;
        for (int i = 0; i < 26; i++) {
            if (cnt[i] != 0) a++;
        }
        for (int l = 0, r = 0, b = 0; r < n; r++) {
            // 往窗口增加字符，进行词频的抵消操作，如果抵消后词频为 0，说明有一个新的字符词频与 p 完全相等
            if (--cnt[s.charAt(r) - 'a'] == 0) b++; 
            // 若窗口长度超过规定，将窗口左端点右移，执行词频恢复操作，如果恢复后词频为 1（恢复前为 0），说明少了一个词频与 p 完全性相等的字符
            if (r - l + 1 > m && ++cnt[s.charAt(l++) - 'a'] == 1) b--;
            if (b == a) ans.add(l);
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [30.串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)

30.串联所有单词的子串
题解：

- 题意分析：与上题的差别在于本题虽然也给定了串联子串的长度，但要求是给定子串组合成的长串也符合条件，返回结果是符合条件的子串起始索引。
- 难点就在于之前子串就一个，构造哈希表也简单，比较也简单，现在子串存在多个，构造和比较都需要重新设计。
- 本题的以单词为单位做比较的，因此建立的哈希表只需要建立单词和出现次数的映射。
- 滑动窗口：

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        int n = s.length(), m = words.length, w = words[0].length();
        // 统计 words 中「每个目标单词」的出现次数
        Map<String, Integer> map = new HashMap<>();
        for (String word : words) map.put(word, map.getOrDefault(word, 0) + 1);
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < w; i++) {
            // 构建一个当前子串对应 map，统计当前子串中「每个目标单词」的出现次数
            Map<String, Integer> curMap = new HashMap<>();
            // 滑动窗口的大小固定是 m * w
            // 每次将下一个单词添加进 cur，上一个单词移出 cur
            for (int j = i; j + w <= n; j += w) {   
                String cur = s.substring(j, j + w);
                if (j >= i + (m * w)) {
                    int idx = j - m * w;
                    String prev = s.substring(idx, idx + w);
                    if (curMap.get(prev) == 1) curMap.remove(prev);    
                    else curMap.put(prev, curMap.get(prev) - 1);
                }
                curMap.put(cur, curMap.getOrDefault(cur, 0) + 1);
                // 如果当前子串对应 map 和 words 中对应的 map 相同，说明当前子串包含了「所有的目标单词」，将起始下标假如结果集
                if (map.containsKey(cur) && curMap.get(cur).equals(map.get(cur)) && curMap.equals(map)) {
                    ans.add(j - (m - 1) * w);
                }
            }
        }
        return ans;
    }
}
```

### [76.最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)
题解：

- 题意分析：在原串中寻找一个长度最小的子串，满足出现过目标串的全部字母
- 初步想法：哈希表+滑动窗口；
- 实现问题：每次去对原串字符进行判断时如何去处理 t 中存在重复字符的情况；如何去保证结果长度的最小性。
   - 判断过程直接将所有字母情况都进行比较。
   - 预先定义结果边界`ansLeft`，`ansRight`
- 算法流程： 
   - 定义两个哈希表用于统计字母出现情况：t 字符串为目标字符串，提前统计字母出现情况。
   - 滑动窗口来更新结果：（**窗口大小不固定，while 循环完成**）
      - 枚举 s 子串的右端点 $\textit{right}$（子串最后一个字母的下标），如果子串涵盖 t，就不断移动左端点 $\textit{left}$ 直到不涵盖为止。在移动过程中更新最短子串的左右端点。
```java
class Solution {
    public String minWindow(String s, String t) {
        int m = s.length();
        int ansLeft = -1, ansRight = m;
        int left = 0;
        char[] chs = s.toCharArray();
        // 字符全为英文字母,直接定义大小 128 的哈希表
        int[] countS = new int[128];
        int[] countT = new int[128];
        for (char c : t.toCharArray()) {
            countT[c]++;
        }
        // 滑动窗口
        for (int right = 0; right < m; right++) {
            countS[chs[right]]++;
            while (isCovered(countS, countT)) {
                if (right - left < ansRight - ansLeft) {
                    ansLeft = left;
                    ansRight = right;
                }
                countS[chs[left++]]--;
            }
        }
        return ansLeft < 0 ? "" : s.substring(ansLeft, ansRight + 1);
    }

    // 判断当前区域的字符串 s 是否能包含字符串 t
    private boolean isCovered(int[] countS, int[] countT) {
        for (int i = 'A'; i <= 'Z'; i++) {
            if (countS[i] < countT[i]) {
                return false;
            }
        }
        for (int i = 'a'; i <= 'z'; i++) {
            if (countS[i] < countT[i]) {
                return false;
            }
        }
        return true;
    }
}
```

### [567.字符串的排列](https://leetcode.cn/problems/permutation-in-string/)
题解：

- 题意分析：判断 s2 是否包含 s1 的排列，转换下问题：**判断 s2 中是否存在一个连续子数组中的出现的字母与 s1 相同。**
- 启发：连续子数组的长度是固定的$s1.length()$——滑动窗口
- 思路：滑动窗口+哈希表
   - 使用两个哈希表分别存储 s1 的字母的出现情况和 s2 窗口中字母的出现情况
   - 每次滑动时剔除左边界字母，加入右边界字母即可。
   - 滑动时固定窗口大小。
```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int n = s1.length(), m = s2.length();
        // 剪枝
        if (n > m) {
            return false;
        }
        int[] cnt1 = new int[26];
        int[] cnt2 = new int[26];
        for (int i = 0; i < n; ++i) {
            ++cnt1[s1.charAt(i) - 'a'];
            ++cnt2[s2.charAt(i) - 'a'];
        }
        // 剪枝
        if (isEqual(cnt1, cnt2)) {
            return true;
        }
        for (int i = n; i < m; ++i) {
            ++cnt2[s2.charAt(i) - 'a'];
            --cnt2[s2.charAt(i - n) - 'a'];
            if (isEqual(cnt1, cnt2)) {
                return true;
            }
        }
        return false;
    }

    private boolean isEqual(int[] c1, int[] c2) {
        for (int i = 0; i < 26; ++i) {
            if (c1[i] != c2[i]) {
                return false;
            }
        }
        return true;
    }
}
```

# 子串
### [125.验证回文串](https://leetcode.cn/problems/valid-palindrome/)
题解：

- 题意分析：判断当前字符串是否为回文字符串，字符串中存在着大小写字母还有符号字段，大小需要转换成小写，符号字段需要剔除。
- 库函数写法：
```java
class Solution {
    public boolean isPalindrome(String s) {
        StringBuilder sb = new StringBuilder();
        int n = s.length();
        for (char c : s.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                sb.append(Character.toLowerCase(c));
            }
        }
        StringBuilder temp = new StringBuilder(sb).reverse();
        return temp.toString().equals(sb.toString());
    }
}
```

- 同类型的题目：
   - [680.验证回文串 II](https://leetcode.cn/problems/valid-palindrome-ii/)

680.验证回文串 II
题解：

- 题意分析：题目要求判断 s 是否能成为回文串，限制了最多可以从中删除一个字符。
- 进行分类讨论可能存在的情况：
   - 双指针向内收缩时未出现不相等情况——true
   - 双指针向内收缩时出现不相等情况
      - 删左边元素后继续判断是否回文
      - 删右边元素后继续判断是否回文
      - 只有一种情况为 true 结果就为 true
- 思路：直接模拟即可
```java
class Solution {
    public boolean validPalindrome(String s) {
        int low = 0, high = s.length() - 1;
        while (low < high) {
            char c1 = s.charAt(low), c2 = s.charAt(high);
            if (c1 == c2) {
                ++low;
                --high;
            } else {
                return validPalindrome(s, low, high - 1) || validPalindrome(s, low + 1, high);
            }
        }
        return true;
    }

    public boolean validPalindrome(String s, int low, int high) {
        for (int i = low, j = high; i < j; ++i, --j) {
            char c1 = s.charAt(i), c2 = s.charAt(j);
            if (c1 != c2) {
                return false;
            }
        }
        return true;
    }
}
```

# 普通数组
### [53.最大子数组和](https://leetcode.cn/problems/maximum-subarray/)
题解：

- 题意分析：要求从给定的数组中求得一个连续子数组满足数组之和最大，关键在给出的示例中——给定数组中存在负数。
- 算法思想：前缀和思想+基于贪心思考：如果加的正数或零显然数组之和再增大，如果加的是负数，不一定就需要重新更新起始点，只有前缀和为负时说明当前序列一定不满足要求，需要重新开始计数
- 算法流程：边计算前缀和，边更新结果，因为使用一个变量保存前缀和，因此每次累计一个位置就去更新结果情况。
```java
// 贪心思路
class Solution {
    public int maxSubArray(int[] nums) {
        int sum = 0;
        int ans = Integer.MIN_VALUE;
        for (int num : nums) {
            // 1. 计算前缀和
            sum += num;
            // 2. 更新结果
            ans = Math.max(ans, sum);
            // 3. 更新起点
            if (sum < 0) {
                sum = 0;
            }
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)
152. 乘积最大子数组

题解：

- 题意分析：给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。
- 与上题的差别：这里的乘积是存在符号情况的，这会导致遍历过程中仅仅通过值来更新并不合理，因为负数绝对值越大数越小，但乘上一个负数后大小反而增大。
- 针对上述差别，可以保存正和负分别对应的边界值，然后边计算边更新结果
```java
class Solution {
    public int maxProduct(int[] nums) {
        int ans = Integer.MIN_VALUE;
        // max: 正向大值; min: 负向大值
        int max = 1, min = 1;
        for (int x : nums) {
            // 简化符号转换: 通过转换 max 和 min 的值实现
            if (x < 0) {
                int temp = max;
                max = min;
                min = temp;
            }
            // 要求子数组连续, 每次与当前值判断是否需要更新
            max = Math.max(x, x * max);
            min = Math.min(x, x * min);
            ans = Math.max(ans, max);
        }
        return ans;
    }
}
```


### [304.二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)
题解：

- 题意分析：要求设计根据矩阵的左上角和右下角坐标计算矩阵和
- 思路一：一维前缀和，构造矩阵每行的前缀和数组，在计算和时时间复杂度为 $O(m)$
```java
class NumMatrix {
    private List<int[]> list;

    public NumMatrix(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        list = new ArrayList<>(m);
        int[] prefixSum;
        for (int i = 0; i < m; i++) {
            prefixSum = new int[n];
            prefixSum[0] = matrix[i][0];
            for (int j = 1; j < n; j++) {
                prefixSum[j] = prefixSum[j - 1] + matrix[i][j];
            }
            list.add(prefixSum);
        }
    }

    // O(m)
    public int sumRegion(int row1, int col1, int row2, int col2) {
        int ans = 0;
        for (int i = row1; i <= row2; i++) {
            int[] prefix = list.get(i);
            ans += col1 == 0 ? prefix[col2] : prefix[col2] - prefix[col1 - 1];
        }
        return ans;
    }
}
```

- 思路二：将一维前缀和拓展为二维前缀和，构造前缀和数组时间复杂度不变$O(m*n)$，计算和复杂度降为$O(1)$
- 这里的 $sum[i+1][j+1]$的定义为：左上角元素为$a[0][0]$右下角元素为$a[i][j]$的子矩阵元素和
```java
class NumMatrix {
    int[][] sums;

    public NumMatrix(int[][] matrix) {
        int m = matrix.length;
        if (m > 0) {
            int n = matrix[0].length;
            sums = new int[m + 1][n + 1];
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    sums[i + 1][j + 1] = sums[i][j + 1] + sums[i + 1][j] - sums[i][j] + matrix[i][j];
                }
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        // 左上角[row1, col1] 右下角[row2, col2]
        return sums[row2 + 1][col2 + 1] - sums[row1][col2 + 1] - sums[row2 + 1][col1] + sums[row1][col1];
    }
}
```

- 同类型的题目：
   - [1277. 统计全为 1 的正方形子矩阵](https://leetcode.cn/problems/count-square-submatrices-with-all-ones/)
   - [1504. 统计全 1 子矩形](https://leetcode.cn/problems/count-submatrices-with-all-ones/)
1277. 统计全为 1 的正方形子矩阵
1504. 统计全 1 子矩形

### [56.合并区间](https://leetcode.cn/problems/merge-intervals/)
题解：

- 题意分析：合并区间的要求：出现重叠；
- `intervals[i][0]=start,intervals[i][1]=end`
- 合并区间时有两种情况：1.区间为重叠区间，需要合并区间；2.区间为不重叠，需要加入结果集，并更新区间。
- 注意当循环结束时，需要将最后一个区间加入结果集中。
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        List<int[]> res = new ArrayList<>();
        Arrays.sort(intervals, (o1, o2) -> o1[0] - o2[0]);
        int left = intervals[0][0];
        int right = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            if (right >= intervals[i][0]) {
                right = Math.max(right, intervals[i][1]);
            } else {
                res.add(new int[]{left, right});
                left = intervals[i][0];
                right = intervals[i][1];
            }
        }
        res.add(new int[]{left, right});
        return res.toArray(new int[res.size()][]);
        // return res.toArray(int[][]::new);
    }
}
```

### [189. 轮转数组](https://leetcode.cn/problems/rotate-array/)
题解：

- 题意分析：题目要求是实现数组元素的循环。
- 题目中的隐含条件：k 是可能超过数组长度的，因此需要进行取余 `k%n`；无返回值那么要求对原数组进行修改
- 思路一：使用辅助数组：利用循环前后下标关系$(i+k) \% len = i$
```java
class Solution {
    public void rotate(int[] nums, int k) {
        int len = nums.length;
        int[] temp = Arrays.copyOf(nums, len);
        for (int i = 0; i < nums.length; i++) {
            nums[(i + k) % len] = temp[i]; 
        }
    }
}
```

- 思路二：反转数组的思路：先反转整体，然后反转前后两个部分（空间复杂度为$O(1)$）
```java
class Solution {
    public void rotate(int[] nums, int k) {
        int len = nums.length;
        int newK = k % len;
        reverse(nums, 0, len - 1);
        reverse(nums, 0, newK - 1);
        reverse(nums, newK, len - 1);
    }

    private void reverse(int[] nums, int left, int right) {
        while (left < right) {
            int temp = nums[left];
            nums[left++] = nums[right];
            nums[right--] = temp;
        }
    }
}
```

- 同类型的题目：
   - [61.旋转链表](https://leetcode.cn/problems/rotate-list/)

61.旋转链表

题解:
- 题意：旋转链表，将链表每个节点向右移动 `k` 个位置，其实就是找到链表的第k个位置然后将后续部分接到头前面。
- 将任务分解：求出链表长度，将k更新成真正连接的链表长度`newK`，然后使用双指针确定后半链表位置，最后进行拼接即可。
```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (k == 0 || head == null || head.next == null) {
            return head;
        }
        int n = 1;
        ListNode cur = head;
        while (cur.next != null) {
            cur = cur.next;
            n++;
        }
        int newK = n - k % n;
        if (newK == n) {
            return head;
        }
        cur.next = head;
        while (newK-- > 0) {
            cur = cur.next;
        }
        ListNode ans = cur.next;
        cur.next = null;
        return ans;
    }
}
```

### [238. 除自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/)
题解：

- 题意分析：如题目所说，要求返回一个计算了每个除自身之外数组元素乘积的数组。
- 基本思想很清晰，乘积的组成就是当前数左右两边的乘积（不包括自己）
- 思路一：使用辅助数组+当前数左边乘积 * 当前数右边乘积：提前计算好前缀积数组和后缀积数组。
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] preMul = new int[n], sufMul = new int[n];
        preMul[0] = 1;
        sufMul[n - 1] = 1;
        for (int i = 1; i < n; ++i) {
            preMul[i] = nums[i - 1] * preMul[i - 1];
        }
        for (int j = n - 2; j >= 0; --j) {
            sufMul[j] = nums[j + 1] * sufMul[j + 1]; 
        }
        int[] ans = new int[n];
        for (int i = 0; i < n; ++i) {
            ans[i] = preMul[i] * sufMul[i];
        }
        return ans;
    }
}
```

- 思路二：优化直接使用结果数组：左边乘积直接保存在结果数组，右边乘积使用变量保存，分两次遍历，第二次遍历过程中与结果数组元素相乘。
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int preMul = 1, sufMul = 1;
        int[] ans = new int[n];
        ans[0] = 1;
        ans[n - 1] = 1;
        for (int i = 1; i < n; ++i) {
            preMul *= nums[i - 1];
            ans[i] = preMul;
        }
        for (int i = n - 2; i >= 0; --i) {
            sufMul *= nums[i + 1];
            ans[i] *= sufMul;
        }
        return ans;
    }
}
```

### [41.缺失的第一个正数](https://leetcode.cn/problems/first-missing-positive/)
题解：

- 题意分析：要求找出数组中没有出现的最小正整数($1, 2, \dots , \infty$）
- 常见的思路：排序+统计（时$O(nlog(n))$，空$O(logn)$)，哈希表+统计（时$O(n)$，空$O(n)$)
- 关键条件：可能缺失的正数只有可能在$1 \sim n+1$中出现。
- 时$O(n)$，空$O(1)$的解法：原地哈希——基于关键条件得出的
   - 把1这个数放到下标为 0的位置， 2这个数放到下标为 1的位置，按照这种思路整理一遍数组。
   - 然后我们再遍历一次数组，第1个遇到的它的值不等于下标的那个数，就是我们要找的缺失的第一个正数。
- 整体流程就是第一步先假设排序：将数组排序成$[1,2,3,\dots]$的形式，然后从左向右遍历，如果发现实际的数组和预先假设排序的数组值不对应，说明出现缺失。
```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            // nums[nums[i] - 1] != nums[i]:没有放在正确的位置上也就是 3 应该放在下标 2 处
            // 重排数组: 0 - 1; 1 - 2; ···
            while (nums[i] >= 1 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums, nums[i] - 1, i);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return n + 1;
    }

    private void swap(int[] nums, int x, int y) {
        int temp = nums[x];
        nums[x] = nums[y];
        nums[y] = temp;
    }
}
```

- 原地哈希同类型的题目：
   - [442.数组中重复的数据](https://leetcode.cn/problems/find-all-duplicates-in-an-array/)
   - [448.找到所有数组中消失的数字](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/)

442.数组中重复的数据

题解:

- 题意分析：与上题的差别在于此题中数组元素可能有重复，要求就是找出出现两次的元素。
- 参考上题中模拟排序/哈希的方式，题目条件中的 $nums[i] \in [1, n]$会使得一次假设排序后，一定存在元素不在预先安排的位置上，只需要找这些元素就是数组中重复的元素。
- 模仿 41 的解法：
```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> ans = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            while (nums[i] >= 1 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums, nums[i] - 1, i);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                ans.add(nums[i]);
            }
        }
        return ans;
    }

    private void swap(int[] nums, int x, int y) {
        int temp = nums[x];
        nums[x] = nums[y];
        nums[y] = temp;
  
    }
}
```

448.找到所有数组中消失的数字

题解:
- 模仿 41 的解法：
```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> ans = new ArrayList<>();
        int n = nums.length;
        for (int i = 0; i < n; ++i) {
            while (nums[i] >= 1 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums, nums[i] - 1, i);
            }
        }
        for (int i = 0; i < n; ++i) {
            if (nums[i] != i + 1) {
                ans.add(i + 1);
            }
        }
        return ans;
    }

    private void swap(int[] nums, int x, int y) {
        int temp = nums[x];
        nums[x] = nums[y];
        nums[y] = temp;
    }
}
```

### [724.寻找数组的中心下标](https://leetcode.cn/problems/find-pivot-index/)
题解：

- 题意分析：计算数组中心下标，中心下标定义为当前位置左右两边数组之和为 0
   - 边界位置左/右数组之和规定为 0
- 思路：直接模拟即可：$leftSum == sum - leftSum - nums[i]$
   - 先计算总和，然后再遍历数组边计算左边和边判断结果
```java
class Solution {
    public int pivotIndex(int[] nums) {
        int sum = 0;
        for (int x : nums) {
           sum += x;
        }      
        int leftsum = 0;
        for (int i = 0; i < nums.length; ++i) {
            if (leftsum == sum - nums[i] - leftsum) {
                return i;
            }
            leftsum += nums[i];          
        }
        return -1;
    }
}
```

# 排序
### [912. 排序数组](https://leetcode.cn/problems/sort-an-array/)
题解：

- 手撕各种常见的排序算法。
```java
class Solution {
    public int[] sortArray(int[] nums) {
        return quickSort(nums, 0, nums.length - 1);
    }
    
    /** 快速排序——递归写法 */
    private int[] quickSort(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        int index = new Random().nextInt(right - left + 1) + left;
        swap(left, index, nums);
        int i = left, j = right;
        int pivot = nums[left];
        while (i < j) {
            while (i < j && nums[j] >= pivot) j--;
            while (i < j && nums[i] <= pivot) i++;
            swap(i, j, nums);
        }
        swap(left, i, nums);
        quickSort(nums, left, i - 1);
        quickSort(nums, i + 1, right);
        return nums;
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
    
    /** 快速排序——划分函数写法 */
    private int partition(int[] nums, int left, int right) {
        int index = new Random().nextInt(right - left + 1) + left;
        swap(left, index, nums);
        int i = left, j = right;
        int pivot = nums[left];
        while (i < j) {
            while (i < j && nums[j] >= pivot) j--;
            while (i < j && nums[i] <= pivot) i++;
            swap(i, j, nums);
        }
        swap(left, i, nums);
        return left; 
    }
    
    /** 归并排序--自顶向下/递归写法 */
    public int[] mergeSort(int[] nums, int left, int right) {
        if (left >= right) {
            return nums;
        }
        int mid = left + (right - left) / 2;
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        return merge(nums, left, right, mid);
    }

    /** 归并排序——自底向上/迭代写法 */
    public void mergeSort(int[] nums) {
        int n = nums.length;
        // 标识当前合并的子数组的大小，从 1 到 n/2
        int size;
        // 标识当前要合并的子数组的起点
        int left;
        for (size = 1; size <= n - 1; size = 2 * size) {
            for (left = 0; left < n - 1; left += 2 * size) {
                int mid = Math.min(left + size - 1, n - 1);
                int right = Math.min(left + 2 * size - 1, n - 1);
                merge(nums, left, right, mid);
            }
        }
    }

    // 合并两个数组元素并排序: 数组 1 是 [left, mid]; 数组 2 是 [mid + 1, right]
    public int[] merge(int[] nums, int left, int right, int mid) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1;
        int index = 0;
        while (i <= mid && j <= right) {
            if (nums[i] < nums[j]) {
                temp[index++] = nums[i++];
            } else {
                temp[index++] = nums[j++];
            }
        }
        while (i <= mid) {
            temp[index++] = nums[i++];
        }
        while (j <= right) {
            temp[index++] = nums[j++];
        }
        for (int k = 0; k < temp.length; k++) {
            nums[left + k] = temp[k];
        }
        return nums;
    }

    /** 堆排序--大顶堆版本 */
    public int[] heapSort(int[] arr) {
        int n = arr.length;
        int[] nums = Arrays.copyOf(arr, n);
        // 构造一个堆
        buildMaxHeap(nums, n);
        // 调整堆
        for (int i = n - 1; i > 0; i--) {
            swap(nums, i, 0);
            n--;
            heapify(nums, 0, n);
        }
        return nums;
    }

    private void buildMaxHeap(int[] nums, int n) {
        for (int i = n / 2; i >= 0; i--) {
            heapify(nums, i, n);
        }
    }

    // index: 堆顶位置
    private void heapify(int[] nums, int index, int len) {
        int largest = index;
        int left = 2 * index + 1;
        int right = 2 * index + 2;

        if (left < len && nums[left] > nums[largest]) {
            largest = left;
        }
        if (right < len && nums[right] > nums[largest]) {
            largest = right;
        }

        if (largest != index) {
            swap(nums, largest, index);
            heapify(nums, largest, len);
        }
    }
}
```

### [1122.数组的相对排序](https://leetcode.cn/problems/relative-sort-array/)
题解：

- 题意分析：给定两个数组，数组二元素不重复，要求根据数组二元素的相对顺序排序数组一的元素，同时要求未在数组二中出现过的元素需要按照升序放在数组一末尾
- 如何将数组二中元素的相对顺序给表示出来：
   - 可以利用哈希表：元素值为哈希表索引，值的大小来表示相对顺序。
- 优化上述思路：用哈希表来保存 arr1 数组的元素情况，然后通过遍历 arr2 来引导填充结果数组，剩下的元素遍历下哈希表后填充即可。
- 思路：计数排序，使用一个数组保存 $arr1$中元素出现的位置和次数
   - 使用一个新数组去不断统计结果即可。
```java
class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        int n = arr1.length;
        int[] ans = new int[n];
        int len = 0;
        for (int x : arr1) {
            len = Math.max(len, x);
        }
        int[] map = new int[len + 1];
        for (int x : arr1) {
            map[x]++;
        }
        int index = 0;
        for (int x : arr2) {
            while (map[x]-- > 0) {
                ans[index++] = x;
            }
        }
        for (int i = 0; i <= len; i++) {
            while (map[i]-- > 0) {
                ans[index++] = i;
            }
        }
        return ans;
    }
}
```

### [75.颜色分类](https://leetcode.cn/problems/sort-colors/)
题解：

- 题意分析：给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，**原地**对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。
- 荷兰国旗问题：使用类快排思想，每次都去做区间划分
- 思想：
   - 基于循环不变量思考：可以明确的是$[0,left)$一定是 0，$[left,mid)$一定是 1，$[mid,right)$一定是 2
   - 那么就只需要遍历过程中不断交换即可。
   - 如果 $mid=1$则说明位置正确，$mid=0$则说明应该在$[left,mid)$区间，则需要与 left 进行交换，$mid=2$则说明应该在$[mid,right)$区间，则说明需要与 right 进行交换。
- 算法本质上其实是三个指针用来控制填充三个对应值
```java
class Solution {
    public void sortColors(int[] nums) {
        int left = 0, mid = 0, right = nums.length;
        if (nums.length < 2) {
            return;
        }
        // 循环不变量：[0, left) 一定是 0，[left, mid) 一定是 1，[mid, right) 一定是 2
        while (mid < right) {
            if (nums[mid] == 0) {
                swap(nums, left, mid);
                left++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                right--;
                swap(nums, mid, right);
            }
        }
    }

    private void swap(int[] nums, int left, int right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp; 
    }
}
```

### [315. 计算右侧小于当前元素的个数](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/)
题解：

- 题意分析：要求计算每个位置右侧小于当前值的元素个数
- 从暴力方式出发：双重 for 循环统计位置的元素大小情况
```java
class Solution {
    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        List<Integer> ans = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            int count = 0;
            for (int j = i + 1; j < n; j++) {
                if (nums[j] < nums[i]) {
                    count++;
                }
            }
            ans.add(count);
        }
        return ans;
    }
}
```

- 问题：大量重复的统计：右侧元素情况重复统计
- 将问题看成逆序对问题，那么就在并的过程中统计好当前位置的逆序对个数即可。
- 一个很关键的问题：如果简单在归并过程中统计，原数组是会被排序的，这导致后续的统计出错。
   - 需要一个额外数组统计原数组中的情况--再简化，直接就根据数组元素大小对下标进行排序就行
```java
class Solution {
    int[] count;

    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        count = new int[n];
        int[] indices = new int[n];
        for (int i = 0; i < n; i++) {
            indices[i] = i;
        }
        mergeSort(nums, indices, 0, n - 1);
        return Arrays.stream(count).boxed().collect(Collectors.toList());
    }

    private void mergeSort(int[] nums, int[] indices, int left, int right) {
        if (left >= right) {
            return;
        }
        int mid = (right - left) / 2 + left;
        mergeSort(nums, indices, left, mid);
        mergeSort(nums, indices, mid + 1, right);
        merge(nums, indices, left, mid, right);
    }

    private void merge(int[] nums, int[] indices, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;
        int rightCount = 0;
        
        while (i <= mid && j <= right) {
            if (nums[indices[i]] <= nums[indices[j]]) {
                temp[k] = indices[i];
                count[indices[i]] += rightCount;
                i++;
            } else {
                temp[k] = indices[j];
                rightCount++;
                j++;
            }
            k++;
        }
        
        while (i <= mid) {
            temp[k] = indices[i];
            count[indices[i]] += rightCount;
            i++;
            k++;
        }
        
        while (j <= right) {
            temp[k++] = indices[j++];
        }
        
        for (i = left, k = 0; i <= right; i++, k++) {
            indices[i] = temp[k];
        }
    }
}
```

- 同类型的题目：
   - [LCR 170. 交易逆序对的总数](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

LCR 170. 交易逆序对的总数
题解：

- 题意分析：要求计算出数组中的逆序对，也就说当前元素大于右边元素的所有情况。
- 逆序对问题：归并排序，因为在治也就是合并单个数组成一个大数组过程中存在左右元素比较，那么在合并过程中统计结果即可。
- 举个例子：
- ![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1715837523606-61ccf19c-8db0-43fe-ae49-c2bcade21481.png#averageHue=%23bababa&clientId=uf5a3bbf2-8717-4&from=paste&height=351&id=u8f8e1423&originHeight=675&originWidth=727&originalType=binary&ratio=1.9250000715255737&rotation=0&showTitle=false&size=49349&status=done&style=none&taskId=uf09b0f39-9bdb-4758-8d10-07975f1d152&title=&width=377.66232362986267)![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1715837536599-76e217c1-5ef6-4b9e-a68e-c04cc177ad81.png#averageHue=%23bbbaba&clientId=uf5a3bbf2-8717-4&from=paste&height=308&id=u1b91bda7&originHeight=592&originWidth=626&originalType=binary&ratio=1.9250000715255737&rotation=0&showTitle=false&size=28090&status=done&style=none&taskId=ub2ec2c67-22c4-4e43-b6b7-2604cdf1fb7&title=&width=325.19479311182124)
```java
class Solution {
    int ans;

    public int reversePairs(int[] record) {
        ans = 0;
        mergeSort(record, 0 , record.length - 1);
        return ans;
    }

    // 归并排序板子
    private int[] mergeSort(int[] nums, int left, int right) {
        if (left >= right) {
            return nums;
        }
        int mid = (right - left) / 2 + left;
        // 递归处理, 边界为左闭右闭
        mergeSort(nums, left, mid);
        mergeSort(nums, mid + 1, right);
        merge(nums, left, mid, right);
        return nums;
    }

    private int[] merge(int[] nums, int left, int mid, int right) {
        // 合并范围边界为左闭右闭
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1;
        int index = 0;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) {
                temp[index++] = nums[i++];
            } else {
                // 逆序对统计
                ans += mid - i + 1;
                temp[index++] = nums[j++];
            }
        }
        while (i <= mid) {
            temp[index++] = nums[i++];
        }
        while (j <= right) {
            temp[index++] = nums[j++];
        }
        for (int k = 0; k < temp.length; k++) {
            nums[k + left] = temp[k];
        }
        return nums;
    }
}
```

# 矩阵
### [73.矩阵置零](https://leetcode.cn/problems/set-matrix-zeroes/)
题解：

- 题意分析：题目要求矩阵中 0 元素的所在行列的全部元素置为 0
- 基本思想：将 0 元素的所在行列保存下来，然后再进行置 0 处理，思想类似于 BFS；
- 思路一：使用辅助数组保存原矩阵 0 元素的所在行列；
```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int[] row = new int[m];
        int[] col = new int[n];
        // 收集行列信息
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (matrix[i][j] == 0) {
                    row[i]++;
                    col[j]++;
                }
            }
        }
        // 更新列元素
        for (int i = 0; i < m; i++) {
            if (row[i] != 0) {
                for (int j = 0; j < n; ++j) {
                    matrix[i][j] = 0;
                }
            }
        }
        // 更新行元素
        for (int j = 0; j < n; j++) {
            if (col[j] != 0) {
                for (int i = 0; i < m; ++i) {
                    matrix[i][j] = 0;
                }
            }
        }
    }

}
```

- 思路二：不使用辅助数组，使用标记变量完成即可。
```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean flagCol0 = false, flagRow0 = false;
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                flagCol0 = true;
            }
        }
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) {
                flagRow0 = true;
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = matrix[0][j] = 0;
                }
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        if (flagCol0) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
        if (flagRow0) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }
    }
}
```

- 继续优化，使用单标记变量：一个标记第一行 0 情况的布尔值，然后倒序遍历整个矩阵做置 0 操作。
```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        boolean flagCol0 = false;
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                flagCol0 = true;
            }
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = matrix[0][j] = 0;
                }
            }
        }
        for (int i = m - 1; i >= 0; i--) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
            if (flagCol0) {
                matrix[i][0] = 0;
            }
        }
    }
}
```

- 同类型的题目：
   - [289.生命游戏](https://leetcode.cn/problems/game-of-life/)

289.生命游戏
题解：

- 题意分析：要求根据给定的规则来得到重置后的矩阵，也就说特定的位置变为 0 或 1
   - 对值 1 也就是活细胞的规则：根据当前 1 位置的四周 1 值的数量进行判断。
   - 对值 0 也就是死细胞的规则：根据当前 0 位置的四周 1 值的数量进行判断。
- 算法思想和上题类似：先统计存活细胞位置，再遍历更新数组。
   - 这里在统计的过程进行优化：个位表示当前状态，十位表示周围细胞个数。
```java
class Solution {
    int m, n;
    int[][] directions = {{-1, -1}, {-1, 0}, {-1, 1}, {0, -1}, {0, 1}, {1, -1}, {1, 0}, {1, 1}};

    public void gameOfLife(int[][] board) {
        m = board.length;
        n = board[0].length;
        // 先统计当前状态下的存活细胞的个数和位置
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // 当前位置细胞如果存活,周围的情况都需要改变
                if (board[i][j] % 10 == 1) {
                    affect(board, i, j);
                }
            }
        }
        // 根据当前状态计算下一个状态
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                calculate(board, i, j);
            }
        }
    }

    // 个位存储细胞状态,十位存储周围细胞存活个数
    private void affect(int[][] board, int i, int j) {
        for (int[] dict : directions) {
            int x = i + dict[0];
            int y = j + dict[1];
            if (x >= 0 && x < m && y >= 0 && y < n) {
                board[x][y] += 10;
            }
        }
    }

    private void calculate(int[][] board, int i, int j) {
        // 当前位置细胞状态
        int value = board[i][j] % 10;
        // 当前位置周围细胞存活情况
        int live = board[i][j] / 10;
        // live == 3 -> 规则 4
        // value == 1 && live == 2 -> 规则 2
        if (live == 3 || (value == 1 && live == 2)) {
            board[i][j] = 1;
        } else {
            board[i][j] = 0;
        }
    }
}
```

### [54.螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)
题解：

- 题意分析：要求将原矩阵的元素顺时针打印出来
- 直接模拟即可：转圈遍历——从左到右；从上到下；从右到左；从下到上
- 模拟的细节：
   - 将左右上下边界提前定义出来；
   - 固定判断的区间定义，统一闭开性；
   - 确定好何时遍历结束
```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ans = new ArrayList<>();
        int left = 0, right = matrix[0].length - 1;
        int top = 0, down = matrix.length - 1;
        // 固定边界为左闭右闭区间, 每次遍历范围为一层
        while (true) {
            // 从左到右
            for (int i = left; i <= right; i++) {
                ans.add(matrix[top][i]);
            }
            if (++top > down) {
                break;
            }
            // 从上到下
            for (int i = top; i <= down; i++) {
                ans.add(matrix[i][right]);
            }
            if (--right < left) {
                break;
            }
            // 从右到左
            for (int i = right; i >= left; i--) {
                ans.add(matrix[down][i]);
            }
            if (--down < top) {
                break;
            }
            // 从下到上
            for (int i = down; i >= top; i--) {
                ans.add(matrix[i][left]);
            }
            if (++left > right) {
                break;
            }
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [59.螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/)

59.螺旋矩阵 II
题解：

- 与上题的差别在于此题需要边遍历边填数边更新数
```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] ans = new int[n][n];
        int num = 1;
        int left = 0, right = n - 1, top = 0, down = n - 1;
        while (true) {
            // left to right
            for (int i = left; i <= right; i++) {
                ans[top][i] = num++;
            }
            if (++top > down) {
                break;
            }
            // top to bottom
            for (int i = top; i <= down; i++) {
                ans[i][right] = num++;
            }
            if (--right < left) {
                break;
            }
            // right to left
            for (int i = right; i >= left; i--) {
                ans[down][i] = num++;
            }
            if (--down < top) {
                break;
            }
            // bottom to top
            for (int i = down; i >= top; i--) {
                ans[i][left] = num++;
            }
            if (++left > right) {
                break;
            }
        }
        return ans;
    }
}
```

### [48.旋转图像](https://leetcode.cn/problems/rotate-image/)
题解：

- 题意分析：将矩阵的元素顺时针旋转 90 度
- 关键是确定好顺时针旋转 90 度前后的坐标关系：
   - **第**$i$**行元素旋转到第**$n-1-i$**列元素；**
   - **第**$j$**列元素旋转到第**$j$**行元素；**
- 要实现空间复杂度为 $O(1)$只需要用一个变量暂存起点元素即可实现:$temp=A, temp \rightarrow B \rightarrow C \rightarrow D \rightarrow A$(A,B,C,D 分别为矩阵的上下左右四块的元素）
- 优化模拟细节：
   - 一轮旋转了 4 个元素，那么遍历只需要遍历矩阵的左上部分即可。
   - 矩阵大小分奇偶情况，可以统一为$i< \frac{n}{2} 和 j < \lfloor {\frac{n + 1}{2}} \rfloor$
```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n / 2; i++) {
            for (int j = 0; j < (n + 1) / 2; j++) {
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[n - 1 - j][i];
                matrix[n - 1 - j][i] = matrix[n - 1 - i][n - 1 - j];
                matrix[n - 1 - i][n - 1 - j] = matrix[j][n - 1 - i];
                matrix[j][n - 1 - i] = tmp;
            }
        }
    }
}
```

### [240.搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)
题解：

- 题意分析：矩阵的元素大小像一个堆，左上角元素最小，要求查找指定元素。
- 如何利用性质：矩阵到图的思路转换

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40499251/1702964146249-b18318ef-9d4f-4f56-9112-5560bd14ba51.png#averageHue=%23f8f8f8&clientId=ub5d3b5cf-f943-4&from=paste&height=538&id=tFcuT&originHeight=672&originWidth=910&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=114800&status=done&style=none&taskId=u6279637e-cd1e-4378-b542-872e55c7f41&title=&width=728)

- 由上图可以确定出查找的入口是$(0,n-1)$
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        int x = 0, y = n - 1;
        while (x < m && y >= 0) {
            if (matrix[x][y] == target) {
                return true;
            } else if(matrix[x][y] > target) {
                y--;
            } else {
                x++;
            }
        }
        return false;
    }
}
```

# 链表
### [160.相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)
题解：

- 题意分析：本质上就是求两个链表的公共后缀的起点位置
- 既然求找公共后缀，那么就存在遍历两个链表的指针指向同一个节点。
- 思路一：双指针：通过双指针遍历两个链表，相交时返回
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int lenA = getLength(headA), lenB = getLength(headB);
        ListNode curA = headA, curB = headB;
        if (lenA > lenB) {
            for (int i = 0; i < lenA - lenB; i++) {
                curA = curA.next;
            }
        } else {
            for (int i = 0; i < lenB - lenA; ++i) {
                curB = curB.next;
            }
        }
        while (curA != null && curB != null) {
            if (curA == curB) {
                return curA;
            }
            curA = curA.next;
            curB = curB.next;
        }
        return null;
    }

    private int getLength(ListNode head) {
        int len = 0;
        ListNode cur = head;
        while (cur != null) {
            len++;
            cur = cur.next;
        }
        return len;
    }
}
```

- 思路二：交叉遍历：两个遍历链表的指针进行交叉遍历即遍历到链表尾节点时下次遍历从另一个链表头开始
- 思路的可行性：
   - 为什么交叉遍历能让两个链表相交？
      - 设后缀部分长度为$c$ ;A 链表由前部分$a$和后缀部分$c$组成；而 B 链表由前部分$b$和后缀部分$c$组成；如果双指针都各自遍历 AB 链表一次，总移动的距离就是$a+c+b=b+c+a$。
   - 如果不存在后缀，交叉遍历能返回 null 吗？
      - 不存在后缀也就是说$c=0$那么两个遍历后双指针都到达尾节点，此时都为空满足退出循环要求。
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode curA = headA, curB = headB;
        while (curA != curB) {
            curA = curA != null ? curA.next : headB;
            curB = curB != null ? curB.next : headA;
        }
        return curA;
    }
}
```

### [206.反转链表](https://leetcode.cn/problems/reverse-linked-list/)
题解：

- 题意分析：反转链表，返回头节点。
- 思路一：**头插法，借助一个虚拟头节点重新构造链表**
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode dummy = new ListNode();
        ListNode cur = head;
        ListNode rear;
        while (cur != null) {
            rear = cur.next;
            cur.next = dummy.next;
            dummy.next = cur;
            cur = rear;
        }
        return dummy.next;
    }
}
```

- 思路二：**双指针，直接改变指针方向，前驱节点变后继节点**
   - **反转后 pre 为当前链表的头结点，cur 执行当前链表尾结点的后继节点**
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        // 反转链表板子
        ListNode cur = head, pre = null;
        while (cur != null) {
            ListNode rear = cur.next;
            cur.next = pre;
            pre = cur;
            cur = rear;
        }
        return pre;
    }
}
```

- 同类型的题目：
   - [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)
92. 反转链表 II
- 题意分析：与上题的差别在于此题需要反转局部链表
- 利用上题的性质来解决——根据性质，反转特定段后，这段的头知道$pre$，后面接着段知道$cur$，如果提前保存好前一段的尾结点$p0$，那么就能在反转后直接将三段链表连接起来就行。
- 算法流程： 
   - 遍历链表，确定不反转的链表段的尾结点$p0$；
   - 反转指定段链表：改变指针方向，$pre$初始化为空
   - 连接三段链表：`p0.next.next=cur;p0.next=pre`
- 头结点也可能参与反转，因此设置虚拟头结点
```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        // head 节点可能参与翻转，添加上虚拟头结点
        ListNode dummy = new ListNode(-1, head);
        // p0 节点为 索引 left - 1 处节点
        ListNode p0 = dummy;
        for (int i = 0; i < left - 1; i++) {
            p0 = p0.next;
        }
        ListNode pre = null, cur = p0.next;
        // 反转思想：调转指针方向
        for (int i = 0; i < right - left + 1; i++) {
            ListNode rear = cur.next;
            cur.next = pre;
            pre = cur;
            cur = rear;
        }
        // cur 指向 right 位置/反转后链表的后继节点位置
        p0.next.next = cur;
        // pre 指向 反转后链表的头结点位置
        p0.next = pre;
        return dummy.next;
    }
}
```

### [234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)
题解：

- 题意分析：判断当前链表的元素是否是回文的
- 暴力解：保存链表元素到数组中，在数组中判断是否元素回文
- 暴力解没有利用回文串的性质
- 思路一：双指针：利用回文串的性质，快指针指向链表的$\lceil len/2 \rceil$，慢指针指向链表头。逆置后半部分链表，然后遍历前后判断是否相等
   - 具体实现步骤：找到中间结点+原地逆置后半部分链表+判断前后链表是否相等。
```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode mid = getMidNode(head);
        mid = reverse(mid);
        while (mid != null) {
            if (mid.val != head.val) {
                return false;
            }  
            mid = mid.next;
            head = head.next;
        }
        return true;
    }

    private ListNode getMidNode(ListNode head) {
        ListNode fast = head, slow = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    private ListNode reverse(ListNode head) {
        ListNode cur = head, pre = null;
        while (cur != null) {
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```

- 思路二：将反转链表的过程融合在遍历中间节点的过程中。也就是说遍历到中间节点前将链表反转，反转后的 pre 指针就是反转后的链表头结点。
```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode slow = head, fast = head;
        // 用于反转链表, 最终指向反转后头结点
        ListNode cur = null;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            // 移动 slow 的同时反转前半部分链表
            ListNode rear = slow.next;
            slow.next = cur;
            cur = slow;
            slow = rear;
        }
        // 链表为奇数长度
        if (fast != null) {
            slow = slow.next;
        }
        while (slow != null) {
            if (slow.val != cur.val) {
                return false;
            } else {
                slow = slow.next;
                cur = cur.next;
            }
        }
        return true;
    }
}
```

### [141.环形链表](https://leetcode.cn/problems/linked-list-cycle/)
题解：

- 题意分析：要求判断当前链表内是否存在环
- 基本思路：快慢指针遍历链表，如果存在环一定相交
```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (fast == slow)  {
                return true;
            }
        }
        return false;
    }
}
```

- 同类型的题目：
   - [142.环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

142.环形链表 II
题解：

- 题意分析：本题与上题的区别在于要求如果存在环则需要返回入口
- 基本思路：同样是双指针：快慢指针相交时有时候后继节点是环的入口，有时候当前节点是环的入口，联想相交链表结论的推导方式可以知道，慢指针和头指针同时向后移动，**慢指针和头指针相遇时为环的入口**
   - 推导过程：$head$到入口的距离$a$，入口到相遇点的距离$b$，环内相遇点到入口的剩余距离$c$，可以推导出$a=c$
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                while (slow != head) {
                    slow = slow.next;
                    head = head.next;
                }
                return slow;
            }
        }
        return null;
    }
}
```

### [143.重排链表](https://leetcode.cn/problems/reorder-list/)
题解：

- 题意分析：要求将链表$L_0\rightarrow L_1 \rightarrow ... \rightarrow L_{n-1} \rightarrow L_n$转换成$L_0\rightarrow L_n \rightarrow L_1 \rightarrow L_{n-1} \rightarrow L_2 \rightarrow ...$,注意不能单纯的改变值，需要进行实际的节点交换
- 思路：直接进行模拟：
   - 先得到中间节点，然后逆置后半部分，最后合并两个链表即可。
```java
class Solution {
    public void reorderList(ListNode head) {
        ListNode node = getMidNode(head);
        ListNode head2 = reverse(node);
        while (head2.next != null) {
            // 交叉连接链表
            ListNode rear1 = head.next;
            ListNode rear2 = head2.next;
            head.next = head2;
            head2.next = rear1;
            head = rear1;
            head2 = rear2;
        }
    }

    /** 获取链表中间节点(向上取整) */
    private ListNode getMidNode(ListNode node) {
        ListNode slow = node, fast = node;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }

    /** 反转链表 */
    private ListNode reverse(ListNode node) {
        ListNode cur = node, pre = null;
        while (cur != null) {
            ListNode rear = cur.next;
            cur.next = pre;
            pre = cur;
            cur = rear;
        }
        return pre;
    }
}
```

### [21.合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)
题解：

- 题意分析：将两个升序链表合并成一个升序链表
- 基本思路：双指针合并即可
```java
class Solution {
    // 迭代法
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dummy = new ListNode(0, list1);
        ListNode pre = dummy;
        ListNode cur1 = list1;
        ListNode cur2 = list2;
        while (cur1 != null && cur2 != null) {
            if (cur1.val > cur2.val) {
                pre.next = cur2;
                cur2 = cur2.next;
            } else {
                pre.next = cur1;
                cur1 = cur1.next;
            }
            pre = pre.next;
        }
        pre.next = cur1 != null ? cur1 : cur2;
        return dummy.next;
    }
    
    // 递归法
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if (list1 == null) {
            return list2;
        } else if (list2 == null) {
            return list1;
        } else if (list1.val >= list2.val) {
            // list2 为 cur
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        } else {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        }
    }
}
```

### [23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)
题解：

- 题意分析：与上题不同的是本题有 k 个升序列表需要合并。
- 首先暴力的思路是合并两个链表，然后合并好的链表再去第三个链表合并，以此类推——如何简化
- 把$\textit{lists}$ 一分为二（尽量均分），先合并前一半的链表，再合并后一半的链表，然后把这两个链表合并成最终的链表。如何合并前一半的链表呢？我们可以继续一分为二。如此分下去直到只有一个链表，此时无需合并。
- 参考归并排序的思想，分治的处理链表：每次处理$k/2$个链表，不断递归下去，直到链表此时只有一个，再去合并。
- 算法流程： 
   - 定义合并两个有序链表的函数；
   - 定义好分治合并链表的函数，注意这里定义和合并区间为左闭右开，这样的好处是左右递归函数会更简洁同时收集结果位置更好判断。
      - 分：要么不能再分了(right-left=0)，要么分到只剩一个链表(right-left=1)，不然就继续分。
      - 治：合并两个有序链表成一个有序链表。
```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return mergeKLists(lists, 0, lists.length);
    }

    // 分治合并链表数组的函数,[left, right)
    private ListNode mergeKLists(ListNode[] lists, int l, int r) {
        int m = r - l;
        // 空链表直接返回
        if (m == 0) {
            return null;
        }
        // 单个链表直接返回
        if (m == 1) {
            return lists[l];
        }
        // 自低向上合并
        ListNode left = mergeKLists(lists, l, l + m / 2);
        ListNode right = mergeKLists(lists, l + m / 2, r);
        return mergerTwoLists(left, right);
    }


    // 合并两个链表
    private ListNode mergerTwoLists(ListNode A, ListNode B) {
        ListNode dummy = new ListNode(-1, A);
        ListNode cur = dummy;
        while (A != null && B != null) {
            if (A.val < B.val) {
                cur.next = A;
                A = A.next;
            } else {
                cur.next = B;
                B = B.next;
            }
            cur = cur.next;
        }
        cur.next = A != null ? A : B;
        return dummy.next;
    }
}
```

### [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)
题解：

- 题意分析：将两个链表构成的数相加并得到结果构成的新链表，**要注意链表中的数字都是逆序存放的**
- 暴力解：直接将两个链表的各自组成的数字提取出来计算和，然后根据和构造新链表
- 暴力解存在很多不必要的遍历，只有两数相加出现进位时，直接元素相加才会出现错误，那么就**在计算的过程中保存进位，边计算边构造即可。**
- 一些问题： 
   - 如何保存进位：先累加后更新
   - **两个链表长度不一时，空结点如何处理：空结点直接当0加进去。**
   - 怎么得到新链表：每次都**直接构造新节点**
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        // 用于计算进位
        int carry = 0;
        ListNode dummy = new ListNode(-1);
        ListNode cur = dummy;
        // 注意这里退出循环的条件:
        // 1. 两个节点都不存在才行, 后续计算中即使节点不存在也会当 0 用
        // 2. 进位完全为 0 才行,如果在遍历完两个链表后进位不为 0 就需要新增一个节点保存进位
        while (l1 != null || l2 != null || carry != 0) {
            if (l1 != null) {
                carry += l1.val;
            }
            if (l2 != null) {
                carry += l2.val;
            }
            // 低位保存在当前新增的节点中
            ListNode rear = new ListNode(carry % 10);
            cur.next = rear;
            cur = cur.next;
            // 高位传递到下一个节点
            carry /= 10;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        return dummy.next;
    }
}
```

- 同类型的题目：
   - [445. 两数相加 II](https://leetcode.cn/problems/add-two-numbers-ii/)
445. 两数相加 II
- 题意分析：与上题的差别在于这题数是从左向右存储的，也就是说要逆置链表得到原本的数
- 本质上就是反转链表+两数相加
- 算法流程：
   - 先逆置两个链表，然后基于两数相加得到新链表，然后逆置后得到结果。
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        l1 = reverseList(l1);
        l2 = reverseList(l2);
        ListNode l3 = addTwo(l1, l2);
        return reverseList(l3);
    }

    // 206.反转链表
    public ListNode reverseList(ListNode head) {
        ListNode dummy = new ListNode(-1);
        ListNode cur = head;
        ListNode rear = null;
        while (cur != null) {
            rear = cur.next;
            cur.next = dummy.next;
            dummy.next = cur;
            cur = rear;
        }
        return dummy.next;
    }

    // 2.两数相加
    public ListNode addTwo(ListNode l1, ListNode l2) {
        int carry = 0;
        ListNode dummy = new ListNode(-1);
        ListNode cur = dummy;
        while (l1 != null || l2 != null || carry != 0) {
            if (l1 != null) {
                carry += l1.val;
            }
            if (l2 != null) {
                carry += l2.val;
            }
            ListNode rear = new ListNode (carry % 10);
            cur.next = rear;
            cur = cur.next;
            carry /= 10;
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        return dummy.next;
    }
}
```

### [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
题解：

- 题意分析：要求删除链表倒数的第 N 个节点；
- 使用前后指针思想，确定待删除结点位置。前后指针之间的距离始终保持为$N$
```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1, head);
        ListNode slow = dummy, fast = head;
        for (int i = 0; i < n; i++) {
            fast = fast.next;
        }
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

### [25.K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)
题解：

- 题意分析：要求将一个链表分组翻转，每 k 个为一组，如果链表长度不足 k 个就不反转（需要求出链表长度）
- 直接模拟即可：遍历过程中每 k 个处理一次，处理逻辑是**先反转当前组的链表，然后更新指针准备下一轮遍历**；
   - 反转链表：使用双指针方式，**反转结束后 pre 为反转后链表的头结点位置，cur 为反转后链表的尾结点的后继节点位置**
   - 在每次反转k个结点后，**将p0更新为下一组需要反转的链表结点。** 
   - 算法流程多一步：拼接并更新$p0$位置
   - 怎么处理链表长度不足 k 个的情况：使用 for 循环完成。
```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        // 1. 计算链表长度
        int len = 0;
        ListNode cur = head;
        while (cur != null) {
            len++;
            cur = cur.next;
        }
        cur = head;
        // 结果为 dummy.next
        ListNode dummy = new ListNode(-1, head);
        // 用于连接每段链表
        ListNode start = dummy;
        // 用于反转链表
        ListNode pre = null;
        // 2. k 个一组反转链表
        for (; len >= k; len -= k) {
            // 3. 反转一组链表
            for (int i = 0; i < k; i++) {
                ListNode rear = cur.next;
                cur.next = pre;
                pre = cur;
                cur = rear;
            }
            // 4. 将第 k 组链表和第 k - 1 组链表进行拼接
            ListNode rear = start.next;	// start 是实际遍历链表 cur 指针
            start.next.next = cur;	// cur 下一段的 head
            start.next = pre;	// pre 当前段的 head
            start = rear;
        }
        return dummy.next;
    }
}
```

- 同类型的题目：
   - [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
24. 两两交换链表中的节点
- 直接套用上题的代码即可

### [138. 复制带随机指针的链表](https://leetcode.cn/problems/copy-list-with-random-pointer/)
题解：

- 题意：完整的复制原链表，深拷贝，要求所有的节点都是直接新建出来的。
   - 为什么不能直接边遍历边复制：节点的当前值可以复制，后继节点复制可以通过遍历后继节点新建得到，但节点的随机指针指向不确定的节点，没办法通过直接赋值或者遍历后新建节点得到。
- 怎么样拿到每个节点随机指针的信息：通过一次遍历将结果存到哈希表中
- 算法流程：两次遍历过程。
   - 首先遍历原链表，每遍历到一个节点，都新建一个相同val的节点，然后使用HashMap存放原链表到新链表的键值对。
   - 第二次遍历时通过HashMap，建立新链表节点之间的next和random关系。
- 本质上就是因为随机指针使得直接一次遍历完不成全部复制的功能，新链表随机指针的生成又依赖于原链表，因此可以使用哈希表建立新旧链表的映射关系。
```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }
        Node cur = head;
        // key:原链表节点;value:新链表节点
        HashMap<Node, Node> map = new HashMap<>();
        // 第一次遍历:生成节点
        while (cur != null) {
            map.put(cur, new Node(cur.val));
            cur = cur.next;
        }
        cur = head;
        // 第二次遍历:连接节点
        while (cur != null) {
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        return map.get(head);
    }
}
```

### [148. 排序链表](https://leetcode.cn/problems/sort-list/)
题解：

- 题意分析：对链表进行排序
- 难点在于链表没有随机访问的特性，直接对链表排序的时间复杂度至少都在$O(n)$以上
- 思路一：暴力解，使用额外空间存储：保存链表元素后排序，再重新赋值
- 思路二：利用常见的排序算法完成排序：
- 利用归并排序思想：自顶向下空间复杂度是log级别，优化为自低向上
- 需要了解的前置知识[206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)，[876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)，[21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)，和归并排序自顶向下；[25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)，和归并排序自低向上
- 算法流程：
1. 分
- 找到当前链表的中间节点，然后向两边继续进行分。
2. 治
- 合并两个链表。
```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode middle = getMiddle(head);
        ListNode newHead = middle.next;
        middle.next = null;

        ListNode left = sortList(head);
        ListNode right = sortList(newHead);

        return merge(left, right);
    }

    private ListNode getMiddle(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    private ListNode merge(ListNode left, ListNode right) {
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;

        while (left != null && right != null) {
            if (left.val < right.val) {
                cur.next = left;
                left = left.next;
            } else {
                cur.next = right;
                right = right.next;
            }
            cur = cur.next;
        }

        if (left != null) {
            cur.next = left;
        }

        if (right != null) {
            cur.next = right;
        }

        return dummy.next;
    }
}
```

- 优化版：递归转迭代，也就是从自低向上变为自顶向下。
```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null) {
            return head;
        }
        int length = 0;
        ListNode node = head;
        while (node != null) {
            length++;
            node = node.next;
        }
        ListNode dummyHead = new ListNode(0, head);
        for (int subLength = 1; subLength < length; subLength <<= 1) {
            ListNode prev = dummyHead, curr = dummyHead.next;
            while (curr != null) {
                ListNode head1 = curr;
                for (int i = 1; i < subLength && curr.next != null; i++) {
                    curr = curr.next;
                }
                ListNode head2 = curr.next;
                curr.next = null;
                curr = head2;
                for (int i = 1; i < subLength && curr != null && curr.next != null; i++) {
                    curr = curr.next;
                }
                ListNode next = null;
                if (curr != null) {
                    next = curr.next;
                    curr.next = null;
                }
                ListNode merged = merge(head1, head2);
                prev.next = merged;
                while (prev.next != null) {
                    prev = prev.next;
                }
                curr = next;
            }
        }
        return dummyHead.next;
    }

    public ListNode merge(ListNode head1, ListNode head2) {
        ListNode dummyHead = new ListNode(0);
        ListNode temp = dummyHead, temp1 = head1, temp2 = head2;
        while (temp1 != null && temp2 != null) {
            if (temp1.val <= temp2.val) {
                temp.next = temp1;
                temp1 = temp1.next;
            } else {
                temp.next = temp2;
                temp2 = temp2.next;
            }
            temp = temp.next;
        }
        if (temp1 != null) {
            temp.next = temp1;
        } else if (temp2 != null) {
            temp.next = temp2;
        }
        return dummyHead.next;
    }
}
```

### [430.扁平化多级双向链表](https://leetcode.cn/problems/flatten-a-multilevel-doubly-linked-list/)
题解：

- 题意分析：目标是让原链表的全部 child 指针置为 null。child 后连接的部分优先连接到当前节点后面。
- 思路：将整体看成一棵树，child 指针为左孩子指针，next 指针为右孩子指针
   - **通过先序遍历不断去构造结果链表**
   - **头结点也有可能需要操作，因此添加虚拟头结点**
```java
class Solution {
    Node ans = new Node();
    Node cur = ans;

    public Node flatten(Node head) {
        if (head == null) {
            return head;
        }
        preOrder(head);
        // 预先设置了虚拟头结点, 因此结果为 ans=ans.next
        ans = ans.next;
        ans.prev = null;
        return ans;
    }
    
    // 通过先序遍历不断构造结果链表
    private void preOrder(Node head) {
        if (head == null) {
            return;
        }
        // 保存左右指针防止断链
        Node left = head.child, right = head.next;
        // 连接新节点
        head.prev = cur;
        head.child = null;
        cur.next = head;
        head.next = null;
        cur = head;
        
        preOrder(left);
        preOrder(right);
    }
}
```

### [146.LRU 缓存](https://leetcode.cn/problems/lru-cache/)
题解：

- 题意分析：实现满足 LRU 缓存算法的数据结构,实现 LRUCache 类：
   - `LRUCache(int capacity)` 以 正整数 作为容量 `capacity` 初始化 LRU 缓存
   - `int get(int key)` 如果关键字 `key` 存在于缓存中，则返回关键字的值，否则返回 -1 。
   - `void put(int key, int value)` 如果关键字 `key` 已经存在，则变更其数据值 `value` ；如果不存在，则向缓存中插入该组 `key-value` 。如果插入操作导致关键字数量超过 `capacity` ，则应该 逐出** 最久未使用**的关键字。
   - 函数 `get` 和 `put` 必须以 $O(1)$ 的平均时间复杂度运行。
- 也就是说 put 方法要满足 LRU 的要求。
   - 想象有一摞书，那么 get 操作就是从书堆中抽一本然后放上面，put 操作就是放上一本新书，但注意如果这本书已经存在过，就把它抽出来放在最上面（原本的书就被替换掉了），如果书堆太高了放不下了，就从最底下抽一本走。
- 从上述流程中可以看出一些重要的点：
   - 需要设计一个数据结构来保存 key-value 对。
   - get 和 put 操作分别需要对书堆顶部和底部进行操作，使用双向链表达到常数级复杂度。
   - put 操作中可能会需要更新缓存值，而且为了满足 LRU 算法要求，还需要同时更新缓存值在链表中的位置，那么需要一个 map 结构来保存 key 和链表节点的对应关系。
- 整体思路：
   - 数据结构选择：一个双向链表；一个保存 key 和节点位置关系的 map；因为头节点可能参与 put 操作，为了简化操作而创建的虚拟头结点。
   - 将 get 操作和 put 操作分解各个小操作：
   - get 操作：
      - 根据 key 拿到对应的 node（**易错点：这里拿到 node 后还需要调整 node 的位置，不然就违背了 LRU 算法了**）
      - 把 node 放到链表头
   - put 操作：
      - 根据 key 拿到对应的 node
      - 把 node 放到链表头
      - 把链表尾部节点删除——将双向链表优化为循环双向链表（易错点：不但要在链表中将节点删除，map 中也要删）
- 总结：LRU 算法的核心点在于每次访问元素后都会去更新元素在链表中的位置，更新策略也很简单：**最新访问的元素在头部，每次淘汰都选尾部的元素淘汰。**
```java
public class LRUCache {
    private static class Node {
        int key, value;
        Node prev, next;

        Node(int k, int v) {
            this.key = k;
            this.value = v;
        }
    }

    private final int capacity;
    // 哨兵节点
    private final Node dummy = new Node(0, 0);
    // 保存key和链表中结点的映射
    private final Map<Integer, Node> keyToNode = new HashMap<>();

    /**
     * 以 正整数 作为容量 capacity 初始化 LRU 缓存
     */
    public LRUCache(int capacity) {
        this.capacity = capacity;
        dummy.prev = dummy;
        dummy.next = dummy;
    }

    /**
     * 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1
     */
    public int get(int key) {
        Node node = getNode(key);
        return node != null ? node.value : -1;
    }

    /**
     * 如果关键字 key 已经存在，则变更其数据值 value;
     * 如果不存在，则向缓存中插入该组 key-value.
     * 如果插入操作导致关键字数量超过 capacity,则应该 逐出 最久未使用的关键字。
     */
    public void put(int key, int value) {
        Node node = getNode(key);
        if (node != null) {
            node.value = value;
            return;
        }
        node = new Node(key, value);
        keyToNode.put(key, node);
        pushFront(node);
        if (keyToNode.size() > capacity) {
            // 最久未访问节点
            Node backNode = dummy.prev;
            keyToNode.remove(backNode.key);
            remove(backNode);
        }
    }

    /**
     * 得到 key 对应的结点同时置于链表头
     */
    private Node getNode(int key) {
        if (!keyToNode.containsKey(key)) {
            return null;
        }
        Node node = keyToNode.get(key);
        remove(node);
        pushFront(node);
        return node;
    }

    /**
     * 删除一个节点
     */
    private void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    /**
     * 在链表头添加一个节点
     */
    private void pushFront(Node node) {
        node.prev = dummy;
        node.next = dummy.next;
        node.prev.next = node;
        node.next.prev = node;
    }
}
```

- 也可以使用有序的 LinkedHashMap 来简化，LinkedHashMap 的 key 根据插入顺序排序，自带双向链表，同样需要注意之前提到的点。
```java
class LRUCache {

    int capacity;
    Map<Integer, Integer> map = new LinkedHashMap<>();

    public LRUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        Integer value = map.get(key);
        map.remove(key);
        map.put(key,value);
        return value;
    }

    // 对于 LinkedHashMap 结构的 put 操作选择先删后增
    public void put(int key, int value) {
        if (map.remove(key) != null) { 
            map.put(key, value); 
            return;
        }
        if (map.size() == capacity) {
            int oldestKey = map.keySet().iterator().next();
            map.remove(oldestKey);
        }
        map.put(key, value);
    }
}
```

- 拓展：设计一个带过期时间的 LRU 缓存。


### [460. LFU 缓存](https://leetcode.cn/problems/lfu-cache/)
题解：

- 题意分析：实现 LFUCache 类：
   - `LFUCache(int capacity)` - 用数据结构的容量 capacity 初始化对象
   - `int get(int key)` - 如果键 key 存在于缓存中，则获取键的值，否则返回 -1 。
   - `void put(int key, int value)` - 如果键 key 已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量 capacity 时，则应该在插入新项之前，移除最不经常使用的项。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最久未使用 的键。
   - 为了确定最不常使用的键，可以为缓存中的每个键维护一个 使用计数器 。使用计数最小的键是最久未使用的键。
   - 当一个键首次插入到缓存中时，它的使用计数器被设置为 1 (由于 put 操作)。对缓存中的键执行 get 或 put 操作，使用计数器的值将会递增。
   - 函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。
- 思路：
- 总结：
   - LFU 算法的核心在于它是根据数据访问次数来淘汰数据的，那么本题在设计的时候就维护了一个访问次数，再将同访问次数的数据按链表组织，就满足了第二个条件：当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最久未使用 的键。
   - 需要注意的点还是：
      - get 操作需要更新访问次数和对应位置，同时还需要注意边界情况（当前访问次数下没有节点）
      - put 操作在删除数据时删除的位置是最少访问次数（一个额外遍历）的链表。
```java
class LFUCache {
    private static class Node {
        int key, value, freq = 1; // 新书只读了一次
        Node prev, next;

        Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private final int capacity;
    private final Map<Integer, Node> keyToNode = new HashMap<>();
    private final Map<Integer, Node> freqToDummy = new HashMap<>();
    private int minFreq;

    public LFUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        Node node = getNode(key);
        return node != null ? node.value : -1;
    }

    public void put(int key, int value) {
        Node node = getNode(key);
        if (node != null) { // 有这本书
            node.value = value; // 更新 value
            return;
        }
        if (keyToNode.size() == capacity) { // 书太多了
            Node dummy = freqToDummy.get(minFreq);
            Node backNode = dummy.prev; // 最左边那摞书的最下面的书
            keyToNode.remove(backNode.key);
            remove(backNode); // 移除
            if (dummy.prev == dummy) { // 这摞书是空的
                freqToDummy.remove(minFreq); // 移除空链表
            }
        }
        node = new Node(key, value); // 新书
        keyToNode.put(key, node);
        pushFront(1, node); // 放在「看过 1 次」的最上面
        minFreq = 1;
    }

    private Node getNode(int key) {
        if (!keyToNode.containsKey(key)) { // 没有这本书
            return null;
        }
        Node node = keyToNode.get(key); // 有这本书
        remove(node); // 把这本书抽出来
        Node dummy = freqToDummy.get(node.freq);
        if (dummy.prev == dummy) { // 抽出来后，这摞书是空的
            freqToDummy.remove(node.freq); // 移除空链表
            if (minFreq == node.freq) {
                minFreq++;
            }
        }
        pushFront(++node.freq, node); // 放在右边这摞书的最上面
        return node;
    }

    // 创建一个新的双向链表
    private Node newList() {
        Node dummy = new Node(0, 0); // 哨兵节点
        dummy.prev = dummy;
        dummy.next = dummy;
        return dummy;
    }

    // 在链表头添加一个节点（把一本书放在最上面）
    private void pushFront(int freq, Node x) {
        Node dummy = freqToDummy.computeIfAbsent(freq, k -> newList());
        x.prev = dummy;
        x.next = dummy.next;
        x.prev.next = x;
        x.next.prev = x;
    }

    // 删除一个节点（抽出一本书）
    private void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
    }
}
```

- 同类型的题目：
   - [895.最大频率栈](https://leetcode.cn/problems/maximum-frequency-stack/)
   - [1172.餐盘栈](https://leetcode.cn/problems/dinner-plate-stacks/)

# 二叉树
### [104.二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)
题解：

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        return Math.max(left, right) + 1;
    }
}
```

- 同类型的题目：
   - [111. 二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)
111. 二叉树的最小深度题解：

- 题意分析：与上题相反。
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) return 0;
        else if (root.left == null) return minDepth(root.right) + 1;
        else if (root.right == null) return minDepth(root.left) + 1;
        else return Math.min(minDepth(root.left), minDepth(root.right)) + 1;
    }
}
```

- 方法二：层序遍历
```java
class Solution {
    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        int depth = 1;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                if (node.left == null && node.right == null) {
                    return depth;
                }
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            depth++;
        }
        return depth;
    }
}
```

### [404. 左叶子之和](https://leetcode.cn/problems/sum-of-left-leaves/)
题解：

- 题意分析：要求计算树中全部的左叶子节点之和
- 算法思路：递归
   - 左叶子节点条件：要是当前节点的 left 指针内容，且自身的左右子节点不为空即可。
   - 递归完成：判断下是否为左叶子节点即可。
```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int temp = 0;
        if (root.left != null && root.left.left == null && root.left.right == null) {
            temp = root.left.val;
        }
        int leftSum = sumOfLeftLeaves(root.left);
        int rightSum = sumOfLeftLeaves(root.right);
        return leftSum + rightSum + temp;
    }
}
```

- 思路二：层序遍历
   - 层序遍历过程中判断当前节点的左节点是否是叶子节点即可。
```java
class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        int ans = 0;
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode tempNode = queue.poll();
                if (tempNode.left != null) {
                    if (tempNode.left.left == null && tempNode.left.right == null) {
                        ans += tempNode.left.val;
                    }
                    queue.offer(tempNode.left);
                }
                if (tempNode.right != null) {
                    queue.offer(tempNode.right);
                }
            }
        }
        return ans;
    } 
}
```

### [226.翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)
题解：

- 题意分析：要求翻转二叉树，从示例图来看需要翻转的是整棵子树，那么可以使用递归方式
- 递归方式下翻转就是交换左右指针，那么要求保证左右孩子信息不丢失（类似与链表中的不断链），就需要预先保存好指针。
```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode temp = root.left;
        root.left = invertTree(root.right);
        root.right = invertTree(temp);
        return root;
    }
}
```

### [101.对称二叉树](https://leetcode.cn/problems/symmetric-tree/)
题解：

- 题意分析：判断当前子树是否轴对称
- 算法思想：递归判断，当前节点如果符合要求，就向下继续判断
   - 左右结点是否相等；
   - 左节点的左孩子和右结点的右孩子是否相等；
   - 左节点的右孩子和右结点的左孩子是否相等；
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return isSameTree(root.left, root.right);
    }

    private boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null || q == null) {
            return p == q;
        } 
        return p.val == q.val && isSameTree(p.left, q.right) && isSameTree(p.right, q.left);
    }
}
```

- 同类型的题目：
   - [100. 相同的树](https://leetcode.cn/problems/same-tree/)
100. 相同的树题解：

- 题意分析：给你两棵二叉树的根节点 `p` 和 `q` ，编写一个函数来检验这两棵树是否相同。
   - 判断规则：如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。
- 算法思想：递归，当前节点相等就继续向下判断。
```java
class Solution {
    public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        }
        if (p != null && q != null && q.val == p.val) {
            return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
        } else {
            return false;
        }
    }
}
```

### [543.二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)
题解：

- 题意分析：二叉树的直径本质上就是左子树中最长路径加上右子树最长路径
- 递归方式：直径是$ans=Math.max(ans, left+right)$，其中left和right为左右子树的最长路径；子树的最长路径其实就是节点深度（默认根节点深度为 0）
```java
class Solution {
    private int ans;
    public int diameterOfBinaryTree(TreeNode root) {
        ans = 0;
        depth(root);
        return ans;
    }

    private int depth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = depth(root.left);
        int right = depth(root.right);
        // 每次遍历就更新直径
        ans = Math.max(ans, left + right);
        // 返回结点的深度
        return Math.max(left, right) + 1;
    }
}
```

### [102.二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)
题解：

- 

```java
class Solution {
    // 写法一: 基于一个队列完成
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int n = queue.size();
            List<Integer> vals = new ArrayList<>(n);
            for (int i = 0; i < n; i++) {
                TreeNode node = queue.poll();
                vals.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
            ans.add(vals);
        }
        return ans;
    }

    // 写法二: 基于两个数组/列表完成
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }
        List<TreeNode> cur = List.of(root);
        while (!cur.isEmpty()) {
            int size = cur.size();
            List<TreeNode> next = new ArrayList<>();
            List<Integer> temp = new ArrayList<>(size);
            for (TreeNode node : cur) {
                temp.add(node.val);
                if (node.left != null) {
                    next.add(node.left);
                }
                if (node.right != null) {
                    next.add(node.right);
                }
            }
            cur = next;
            ans.add(temp);
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)
103. 二叉树的锯齿形层序遍历题解：

- 题意分析：给你二叉树的根节点 root ，返回其节点值的 锯齿形层序遍历 。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。
- 与上题的差别在于需要根据节点深度进行不同方向的遍历
- 思路：层序遍历，同时维护一个深度用于控制遍历方向：奇数正向-偶数反向（逆置遍历结果）
```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        int depth = 1;
        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> tempList = new ArrayList<>();
            for (int i = 0; i < size; i++) {
                TreeNode tempNode = queue.poll();
                tempList.add(tempNode.val);
                if (tempNode.left != null) {
                    queue.add(tempNode.left);
                }
                if (tempNode.right != null) {
                    queue.add(tempNode.right);
                }
            }
            if (depth % 2 == 0) {
                Collections.reverse(tempList);
            }
            ans.add(tempList);
            depth++;
        }
        return ans;
    }
}
```

### [108.将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/)
题解：

- 题意分析：给定一个有序的数组，要求将其转换成一个高度平衡的二叉搜索树
- 关键在于高度平衡带来的条件：只要每次都选择数组中间位置构造左右子树，那么左右子树总是平衡的
- 算法流程：每次取中间位置作为根节点，然后递归构造数组左半部分和右半部分
```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
       return sortedArrayToBST(nums, 0, nums.length - 1);
    }
    private TreeNode sortedArrayToBST(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        if (right == left) {
            return new TreeNode(nums[left]);
        }
        int mid = left + (right - left)/2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = sortedArrayToBST(nums, left, mid - 1);
        root.right = sortedArrayToBST(nums, mid + 1, right);
        return root;
    }
}
```

- 同类型的题目：
   - [109.有序链表转换二叉搜索树](https://leetcode.cn/problems/convert-sorted-list-to-binary-search-tree/)  
   109.有序链表转换二叉搜索树
- 题意分析： 给定的原链表已经是按升序排列的了，要求构造出一棵新的平衡二叉树，那么只需要每次确定中间节点为根节点，然后递归处理左右部分即可。
- 基本思想：递归+中序遍历：根据搜索树中序遍历为升序的性质，使用中序遍历边遍历链表边构造二叉树
   - 左右递归操作：递归处理 mid 位置左右的链表节点
   - 节点操作：构造根节点；移动头结点；挂载左右节点
```java
class Solution {
    private ListNode temp;

    public TreeNode sortedListToBST(ListNode head) {
        if (head == null) {
            return null;
        }
        temp = head;
        int len = 0;
        ListNode cur = head;
        while (cur != null) {
            len++;
            cur = cur.next;
        }
        return InOrder(0, len - 1);
    }

    private TreeNode InOrder(int l, int r) {
        if (l > r) {
            return null;
        }
        int mid = l + (r - l) / 2;
        TreeNode left = InOrder(l, mid - 1);
        TreeNode root = new TreeNode(temp.val);
        temp = temp.next;
        root.left = left;
        root.right = InOrder(mid + 1, r);
        return root;
    }
}
```

### [98.验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)
题解：

- 题意分析：验证一棵树是否满足二叉搜索树（中序遍历为有序序列）
- 基本思想：既然二叉搜索树满足中序遍历有序，那么可以直接通过中序遍历，边遍历边判断即可
- 注：本题变量保存使用 long 型，以防止数值溢出
```java
class Solution {
    private long pre = Long.MIN_VALUE;

    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        }
        if (!isValidBST(root.left) || root.val <= pre) {
            return false;
        }
        pre = root.val;
        return isValidBST(root.right);
    }
}
```

- 思考能不能用先序遍历和后序遍历实现判断：
- 先序遍历：利用二叉搜索树基本定义：当前节点大于左子树小于右子树
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean isValidBST(TreeNode root, long left, long right) {
        if (root == null) {
            return true;
        }
        long x = root.val;
        return left < x && x < right && isValidBST(root.left, left, x) && isValidBST(root.right, x, right);
    }
}
```

- 后序遍历：
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return dfs(root)[1] != Long.MAX_VALUE;
    }

    private long[] dfs(TreeNode node) {
        if (node == null)
            return new long[]{Long.MAX_VALUE, Long.MIN_VALUE};
        long[] left = dfs(node.left);
        long[] right = dfs(node.right);
        long x = node.val;
        // 也可以在递归完左子树之后立刻判断，如果发现不是二叉搜索树，就不用递归右子树了
        if (x <= left[1] || x >= right[0])
            return new long[]{Long.MIN_VALUE, Long.MAX_VALUE};
        return new long[]{Math.min(left[0], x), Math.max(right[1], x)};
    }
}
```

### [230.二叉搜索树中第K小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)
题解：

- 题意分析：要求查找二叉搜索树中第 k 个最小元素（从 1 开始计数）
- 基本思想：中序遍历构造出节点列表，然后直接返回第 k 个即可。
```java
class Solution {
    List<Integer> res;
    public int kthSmallest(TreeNode root, int k) {
        res = new ArrayList<>();
        InOrder(root);
        return res.get(k - 1);
    }

    private void InOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        InOrder(root.left);
        res.add(root.val);
        InOrder(root.right);
    }
}
```

### [199.二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)
题解：

- 题意分析：要求返回从右向左看到的二叉树的第一个节点
- 算法思想：既然要求从右向左看到的节点，那么选择中右左遍历顺序，只需要将每一层第一个遍历到的右节点保存在结果集中即可。
   - 层数用 depth 记录，作为递归函数的形参
   - 判断第一个遍历到的右节点：每次遍历新一层都会结果集都会增加一个元素，因此通过大小判断：$depth=res.size()$
```java
class Solution {
    List<Integer> res;
    public List<Integer> rightSideView(TreeNode root) {
        res = new ArrayList<>();
        dfs(root, 0);
        return res;
    }

    private void dfs(TreeNode root, int depth) {
        if (root == null) {
            return;
        }
        if (depth == res.size()) {
            res.add(root.val);
        }
        dfs(root.right, depth + 1);
        dfs(root.left, depth + 1);
    }
}
```

### [114.二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)
题解：

- 题意分析：要求将二叉树展开成"单链表"，也就是 left 指针始终指向 null,而 right 指针指向下一个节点。
- 基本思想：题目说明展开后的单链表应该与二叉树先序遍历顺序相同，那么直接使用先序遍历得到结点序列，然后再重新构造出单链表即可。
```java
class Solution {
    public void flatten(TreeNode root) {
        List<TreeNode> res = new ArrayList<>();
        PreOrder(root, res);
        int size = res.size();
        for (int i = 1; i < size; i++) {
            TreeNode pre = res.get(i - 1);
            TreeNode cur = res.get(i);
            pre.left = null;
            pre.right = cur;
        }
    }

    private void PreOrder(TreeNode root, List<TreeNode> res) {
        if (root == null) {
            return;
        }
        res.add(root);
        PreOrder(root.left, res);
        PreOrder(root.right, res);
    }
}
```

### [105.从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
题解：

- 题意分析：根据给出的前序遍历和中序遍历序列构造出一棵二叉树
- 前序遍历和中序遍历序列的关系：前序遍历序列中根节点清晰，而中序遍历序列中左右子树情况清晰，那么基本思路就是从前序遍历序列中得到根节点，再去中序遍历序列中查找位置后构造左右子树
- 实现的具体细节问题：
   - 在中序遍历序列中查找根节点：如果是根据值一个个遍历时间复杂度较高，可以使用哈希表 map 来进行映射，key 为节点的值，value 为中序遍历序列中出现的位置。
   - 算法流程：先通过遍历二叉树构造出哈希表；再得到根节点，定位根节点，根据长度确定左右子树，然后递归构造左右子树
```java
class Solution {
    private HashMap<Integer, Integer> map;
    private int[] preorder, inorder;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        int n = preorder.length;
        map = new HashMap<>(n);
        for (int i = 0; i < n; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(0, n - 1, 0, n - 1);
    }

    private TreeNode buildTree(int preorderLeft, int preorderRight, int inorderLeft, int inorderRight) {
        if (preorderLeft > preorderRight) {
            return null;
        }
        int preorderRoot = preorderLeft;
        int inorderRoot = map.get(preorder[preorderRoot]);
        TreeNode root = new TreeNode(preorder[preorderRoot]);
        int sizeLeft = inorderRoot - inorderLeft;
        root.left = buildTree(preorderLeft + 1, preorderLeft + sizeLeft, inorderLeft, inorderRoot - 1);
        root.right = buildTree(preorderLeft + sizeLeft + 1, preorderRight, inorderRoot + 1, inorderRight);
        return root;
    }
}
```

- 同类型的题目：
   - [106.从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
   - [889.根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)
   106.从中序与后序遍历序列构造二叉树题解：

- 题意分析：中序+后序也可以唯一确定一棵树
- 思路：与上题的差别在于需要从右往左遍历后序数组
```java
class Solution {
    Map<Integer, Integer> map;
    int[] inorder, postorder;

    public TreeNode buildTree(int[] inorder, int[] postorder) {
        this.inorder = inorder;
        this.postorder = postorder;
        int n = inorder.length;
        map = new HashMap<>(n);
        for (int i = 0; i < n; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(0, n - 1, 0, n - 1);
    }

    private TreeNode buildTree(int inorderLeft, int inorderRight, int postorderLeft, int postorderRight) {
        if (postorderLeft > postorderRight) {
            return null;
        }
        int postorderRoot = postorderRight;
        int inorderRoot = map.get(postorder[postorderRoot]);
        TreeNode root = new TreeNode(inorder[inorderRoot]);
        int sizeRight = inorderRight - inorderRoot;
        root.left = buildTree(inorderLeft, inorderRoot - 1, postorderLeft, postorderRight - sizeRight - 1);
        root.right = buildTree(inorderRoot + 1, inorderRight, postorderRight - sizeRight, postorderRight - 1);
        return root;
    }
}
```
889.根据前序和后序遍历构造二叉树题解：

- 需要明确一点：前序+中序/中序+后序可以唯一确定一棵二叉树，而前序+后序不行
- 思路基本相同
```java
class Solution {
    Map<Integer, Integer> map;
    int[] preorder, postorder;

    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        this.preorder = preorder;
        this.postorder = postorder;
        int n = preorder.length;
        map = new HashMap<>(n);
        for (int i = 0; i < n; i++) {
            map.put(postorder[i], i);
        }
        return constructFromPrePost(0, n - 1, 0, n - 1);
    }

    private TreeNode constructFromPrePost(int preorderLeft, int preorderRight, int postorderLeft, int postorderRight) {
        if (preorderLeft > preorderRight) {
            return null;
        }
        int preorderRoot = preorderLeft;
        TreeNode root = new TreeNode(preorder[preorderRoot]);
        int sizeLeft = 0;
        if (preorderLeft < preorderRight) {
            sizeLeft = map.get(preorder[preorderLeft + 1]) - postorderLeft + 1;
        }
        root.left = constructFromPrePost(preorderLeft + 1, preorderLeft + sizeLeft, postorderLeft, postorderLeft + sizeLeft - 1);
        root.right = constructFromPrePost(preorderLeft + sizeLeft + 1, preorderRight, postorderLeft + sizeLeft, postorderRight - 1);
        return root;
    }
}
```

### [437.路径总和 III](https://leetcode.cn/problems/path-sum-iii/)
题解：

- 题意分析：题目要求计算出二叉树中所有满足节点值之和等于目标值的路径，注意这里的路径对起始节点没有要求，但路径方向必须向下，也就是说不能跨越两棵子树

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40499251/1703386933685-079d5efb-9b35-4e02-9321-2b788b07ce38.png#averageHue=%23f9f9f8&clientId=u15cd5adb-d93f-4&from=paste&height=174&id=cwawx&originHeight=218&originWidth=333&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=16212&status=done&style=none&taskId=u0f2d3a96-ef52-4261-998d-3124036f625&title=&width=266.4)其中的 5，2，2，-2 虽然值满足 8，但跨越了两棵子树，不属于路径

- 基本思想：既然对起始节点没有限制，那么可以尝试暴力搜索：直接 DFS 暴力搜索每个节点存在的路径数目（判断路径数目同样需要 DFS）
   - 算法流程：定义以当前节点为起始节点的计算路径的 DFS 函数，定义枚举二叉树每个节点并统计总路径数的 DFS 函数
```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        if (root == null) {
            return 0;
        }
        int res = rootSum(root, targetSum);
        res += pathSum(root.left, targetSum);
        res += pathSum(root.right, targetSum);
        return res;
    }

    private int rootSum(TreeNode root, long targetSum) {
        int res = 0;
        if (root == null) {
            return 0;
        }
        int val = root.val;
        if (val == targetSum) {
            res++;
        }
        res += rootSum(root.left, targetSum - val);
        res += rootSum(root.right, targetSum - val);
        return res;
    }
}
```

- 优化方向：双重 DFS 的问题在于**重复计算，题目中有个关键条件上述解法没有用到：路径只能向下。**
```java
class Solution {
    private Map<Long, Integer> map = new HashMap<>();
    private int ans, targetSum;

    public int pathSum(TreeNode root, int targetSum) {
        this.targetSum = targetSum;
        if (root == null) {
            return 0;
        }
        map.put(0L, 1);
        dfs(root, root.val);
        return ans;
    }

    private void dfs(TreeNode root, long val) {
        if (map.containsKey(val - targetSum)) {
            ans += map.get(val - targetSum);
        }
        map.put(val, map.getOrDefault(val, 0) + 1);
        if (root.left != null) {
            dfs(root.left, val + root.left.val);
        }
        if (root.right != null) {
            dfs(root.right, val + root.right.val);
        }
        map.put(val, map.getOrDefault(val, 0) - 1);
    }
}
```

- 同类型的题目：
   - [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)
   - [113. 路径总和 II](https://leetcode.cn/problems/path-sum-ii/)
112. 路径总和
113. 路径总和 II

### [236.二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)
题解：

- 题意分析：寻找给定的两个节点的最近公共祖先
- 思路：将存在的情况进行分类讨论，然后递归进行判断即可。
- 关键在于分类讨论各种情况： 递归向下查询
   - 当前节点是空节点：返回当前结点
   - 当前节点是p/q：返回当前结点
   - 其他： 
      - 左右子树都找到:返回当前节点
      - 只有左子树找到:返回递归左子树的结果
      - 只有右子树找到:返回递归右子树的结果
      - 左右子树都没有找到:返回空节点
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        }
        if (left != null) {
            return lowestCommonAncestor(left, p, q);
        }
        return lowestCommonAncestor(right, p, q);
    }
}
```

- 同类型的题目：
   - [235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
235. 二叉搜索树的最近公共祖先题解：

- 题意分析：与上题的差别在于树变为了二叉搜索树
- 基本思想不变，主要的差别在于节点的大小顺序已知，确定了递归方向，可以通过判断当前节点值的情况来去进行递归。
- 同样是分类讨论： 
   - p和q都在左子树：返回递归左子树的结果
   - p和q都在右子树：返回递归右子树的结果
   - p在左子树，q在右子树：返回当前结点
   - 当前结点为p或q：返回当前结点
- 当前节点是否要判空：不需要，一定不为空
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == p || root == q) {
            return root;
        }
        int x = root.val;
        if (p.val < x && q.val < x) {
            return lowestCommonAncestor(root.left, p, q);
        }
        if (p.val > x && q.val > x) {
            return lowestCommonAncestor(root.right, p, q);
        }
        return root;
    }
}
```

### [124.二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)
题解：

- 题意分析：要求找到一条路径上节点和最大的路径，返回的是路径和
- 对起始点没有要求，这说明需要如果是和最大的路径，当前节点加上的左右子路径和也是最大的。
- 算法思想：DFS，从根向下遍历，不断计算左右子路径的最大和，向上归时更新结果即可。
   - 注意：二叉树的节点值可能会负，这会导致越累加越小的情况，因此返回时需要再进行负值判断。
```java
class Solution {
    private int ans = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        dfs(root);
        return ans;
    }

    private int dfs(TreeNode root) {
        if (root == null) {
            return 0;
        }
        // 左子树存在的最大路径和
        int left = dfs(root.left);
        // 右子树存在的最大路径和
        int right = dfs(root.right);
        ans = Math.max(left + right + root.val, ans);
        // 当前子树存在的最大路径和
        return Math.max(Math.max(left, right) + root.val, 0);
    }
}
```

### [814.二叉树剪枝](https://leetcode.cn/problems/binary-tree-pruning/)
题解：

- 题意分析：题目要求将不包含 1 的子树删除
- 包含 1 的子树情况有：当前 node 的后代中存在 1 都属于；
- 需要移除的情况：根节点是 1 但左右孩子中有 0，那么需要删除左右孩子
- 基本思路：后序遍历，处理节点时如果为 0 置为 null，不为 0 向上返回结果即可。
```java
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        if (root == null) return null;
        root.left = pruneTree(root.left);
        root.right = pruneTree(root.right);
        if (root.left != null || root.right != null) return root;
        return root.val == 0 ? null : root;
    }
}

```

### [297.二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)
题解：

- 题意分析：这里的序列化是指将二叉树序列化成一个字符串
- 首先明确一点：序列化和反序列化是可逆操作，这就要求我们
- 思路：

### [129.求根节点到叶节点数字之和](https://leetcode.cn/problems/sum-root-to-leaf-numbers/)
题解：

- 题意分析：计算从根节点到叶节点生成的 所有数字之和 。
- 思路：DFS
   - 什么时候收集结果：遍历到叶子结点时。
   - 遍历过程中的处理：统计数字
```java
class Solution {
    public int sumNumbers(TreeNode root) {
        return dfs(root, 0);
    }

    private int dfs(TreeNode root, int num) {
        if (root == null) {
            return 0;
        }
        int sum = num * 10 + root.val; 
        if (root.left == null & root.right == null) {
            return sum;
        } else {
            return dfs(root.left, sum) + dfs(root.right, sum);
        }
    }
}
```

### [897.递增顺序搜索树](https://leetcode.cn/problems/increasing-order-search-tree/)
题解：

- 题意分析：给你一棵二叉搜索树的 root ，请你 按中序遍历 将其重新排列为一棵递增顺序搜索树，使树中最左边的节点成为树的根节点，并且每个节点没有左子节点，只有一个右子节点。
- 思路：中序遍历，顺序保存下出现的全部结点然后重新构造即可。
```java
class Solution {
    List<TreeNode> list = new ArrayList<>();

    public TreeNode increasingBST(TreeNode root) {
        inOrder(root);
        TreeNode dummy = new TreeNode();
        TreeNode cur = dummy;
        for (TreeNode node : list) {
            cur.right = node;
            node.left = null;
            cur = cur.right;
        }
        return dummy.right;
    }

    private void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        list.add(root);
        inOrder(root.right);
    }
}
```

### [538.把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)
题解：

- 题意分析：要求将 node 的值变为右中左遍历升序的顺序，同时值还是由前缀累计起来的。
- 思路：中序遍历(右中左)，用一个 preSum 保存前缀和
```java
class Solution {
    int preSum;

    public TreeNode convertBST(TreeNode root) {
        preSum = 0;
        inOrder(root);
        return root;
    }
    private void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.right);
        root.val += preSum;
        preSum = root.val;
        inOrder(root.left);
    }
}
```

### [173.二叉搜索树迭代器](https://leetcode.cn/problems/binary-search-tree-iterator/)
题解：
```java
class BSTIterator {
    List<TreeNode> list = new ArrayList<>();
    int index = 0;

    public BSTIterator(TreeNode root) {
        list.add(new TreeNode());
        inOrder(root);
    }

    private void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        list.add(root);
        inOrder(root.right);
    }
    
    public int next() {
        if (hasNext()) {
            int value = list.get(index + 1).val;
            index++;
            return value;
        } else {
            return -1;
        }
    }
    
    public boolean hasNext() {
        if (index == list.size() - 1) {
            return false;
        } else {
            return true;
        }
    }
}
```

### [220.存在重复元素 III](https://leetcode.cn/problems/contains-duplicate-iii/)
题解：

- 题意分析：判断是否存在一个下标二元组满足：下标不重复；下标差值的绝对值小于 indexDiff；下标对应元素差值的绝对值小于 valueDiff
- 对于任意一个位置 i（假设其值为 u），我们其实是希望在下标范围为$[max(0, i - k), i]$内找到值范围在$[u-t, u + t]$的数，其中的 k 就是 indexDiff，t 就是 valueDiff。
- 思路：滑动窗口+红黑树。
   - 使用红黑树来保存窗口中的元素，遍历时不断判断上下界是否符合要求，同时始终保证窗口大小为 indexDiff
- 为什么要用滑动窗口？
   - 每次遍历到任意位置 i 的时候，往后检查 k 个元素。用滑动窗口保存这个前 k 个元素，可以减少重复遍历。
- 为什么要用红黑树？
   - 这里的使用场景是插入和范围查询，红黑树对于二者的时间复杂度都是$O(logn)$级别的。
```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {
        int n = nums.length;
        // 用来保存数组中的元素
        TreeSet<Long> treeSet = new TreeSet<>();
        for (int i = 0; i < n; i++) {
            Long u = nums[i] * 1L;
            // 从 treeSet 中找到小于等于 u 的最大值（小于等于 u 的最接近 u 的数）
            Long lower = treeSet.floor(u);
            // 从 treeSet 中找到大于等于 u 的最小值（大于等于 u 的最接近 u 的数）
            Long upper = treeSet.ceiling(u);
            if (lower != null && u - lower <= valueDiff) {
                return true;
            }
            if (upper != null && upper - u <= valueDiff) {
                return true;
            }
            treeSet.add(u);
            // 维持滑动窗口大小为 indexDiff
            if (i >= indexDiff) {
                treeSet.remove(nums[i - indexDiff] * 1L);
            }
        }
        return false;
    }
}
```

- 时间复杂度：$O(nlogk)$，其中 k 为 indexDiff。
- 时间复杂度更低的方式是基于桶排序完成。上述方法还存在一个问题，就在窗口中找到 u 的上下边界的时间复杂度是 log 级别的。
- 优化方式：将这 k 个元素分到 k 个桶中，那么在窗口中查询边界的时间复杂度是$O(1)$，整体时间复杂度降为线性级别。
- 算法流程：
   - 令桶的大小为 `size=valueDiff+1`，根据 u 计算所在桶编号：
      - 如果已经存在该桶，说明前面已有$[u−t,u+t]$范围的数字，返回 true
      - 如果不存在该桶，则检查相邻两个桶的元素是有$[u−t,u+t]$范围的数字，如有 返回 true
      - 建立目标桶，并删除下标范围不在 $[max(i-k),i)$ 内的桶
- 关键难点在于将元素映射到各个桶中：
1. `size` 为什么是 `valueDiff+1`?
- 映射规则是将当前值与 size 取余得到桶中的下标，
```java
class Solution {
    long size;

    public boolean containsNearbyAlmostDuplicate(int[] nums, int indexDiff, int valueDiff) {
        int n = nums.length;
        size = valueDiff + 1L;
        Map<Long, Long> map = new HashMap<>();
        for (int i = 0; i < n; i++) {
            long u = nums[i];
            long index = getIndex(u);
            if (map.containsKey(index)) {
                return true;
            }
            long lower = index - 1, upper = index + 1;
            if (map.containsKey(lower) && u - map.get(lower) <= valueDiff) {
                return true;
            }
            if (map.containsKey(upper) && map.get(upper) - u <= valueDiff) {
                return true;
            }
            map.put(index, u);
            if (i >= indexDiff) {
                map.remove(getIndex(nums[i - indexDiff]));
            }
        }
        return false;
    }

    // 下标与桶中下标的映射关系
    private long getIndex(long u) {
        return u >= 0 ? u / size : ((u + 1) / size) - 1;
    }
}
```

### [729.我的日程安排表 I](https://leetcode.cn/problems/my-calendar-i/)
题解：

- 题意分析：本质上就是设计一个数据结构能判断每次区间是否出现重叠
- 思路：直接模拟
```java
class MyCalendar {
    List<int[]> list = new ArrayList<>();

    public MyCalendar() {

    }
    
    public boolean book(int start, int end) {
        // [start, end) == [start, end - 1]
        end--;
        for (int[] info : list) {
            int left = info[0], right = info[1];
            // 不重叠
            if (start > right || end < left) {
                continue;
            } else {
                return false;
            }
        }
        list.add(new int[]{start, end});
        return true;
    }
}

```

# 图论
### [200.岛屿数量](https://leetcode.cn/problems/number-of-islands/)
题解：

- 题意分析：题目中对于一个岛屿的判定是上下左右四个方向都被水包围
- 基本思想：染色法——遇到一个没有遍历过的节点陆地，计数器就加一，然后把该节点陆地所能遍历到的陆地都标记上。
- 算法流程：
   - 遍历陆地——双重 for 循环完成，遇到陆地就进行计数，然后进行染色。
   - 染色：通过 dfs 完成，标记当前节点周边的陆地——dfs 遍历并置 0 即可。
```java
class Solution {
    char[][] grid;

    public int numIslands(char[][] grid) {
        this.grid = grid;
        int ans = 0;
        // 1. 遍历陆地
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == '1') {
                    ans++;
                    dfs(i, j);
                }
            }
        }
        return ans;
    }

    // 2. 染色
    private void dfs(int i, int j) {
        // 超出边界或者遇到非陆地直接返回
        if(i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == '0') return;
        // 置零表示已经标记该陆地
        grid[i][j] = '0';
        dfs(i, j - 1);
        dfs(i, j + 1);
        dfs(i + 1, j);
        dfs(i - 1, j);
    }
}
```

- 同类型的题目：
   - [695.岛屿的最大面积](https://leetcode.cn/problems/max-area-of-island/)
   - [1020.飞地的数量](https://leetcode.cn/problems/number-of-enclaves/)
   695.岛屿的最大面积题解：

- 与上题的差别在于此题要求计算岛屿的最大面积，也就是 1 的最大个数
- 基本思路与上题相同，改造下统计结果时机即可——也就是每次判断为岛屿后就重新统计 1 的个数并更新结果。
```java
class Solution {
    int[][] grid;
    int count;

    public int maxAreaOfIsland(int[][] grid) {
        this.grid = grid;
        int ans = 0;
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 1) {
                    count = 0;
                    dfs(i, j);
                    ans = Math.max(count, ans);
                }
            }
        }
        return ans;
    }

    private void dfs(int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
            return;
        }
        grid[i][j] = 0;
        count++;
        dfs(i + 1, j);
        dfs(i - 1, j);
        dfs(i, j + 1);
        dfs(i, j - 1);
    }

}
```
1020.飞地的数量题解： 

- 题意分析：问题转换成没有接触边界的陆地大小，基本条件：如果边界上的 1 不需要判断默认成立。
- 算法思路：从边界出发遍历并染色陆地，如果没被染色到的地方就算孤立的陆地就需要统计，最后遍历下 grid 数组统计下未被染色的个数即可。
```java
class Solution {
    int[][] grid;

    public int numEnclaves(int[][] grid) {
        this.grid = grid;
        int ans = 0;
        int m = grid.length, n = grid[0].length;
        // 遍历左右边界
        for(int i = 0; i < m; i++){
            if(grid[i][0] == 1)
                dfs(i, 0);
            if(grid[i][n - 1] == 1)
                dfs(i, n - 1);
        }
        // 遍历上下边界
        for(int j = 1; j < n - 1; j++){
            if(grid[0][j] == 1)
                dfs(0, j);
            if(grid[m - 1][j] == 1)
                dfs(m - 1, j);
        }

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == 1) {
                    ans++;
                }
            }
        }
        return ans;
    }

    private void dfs(int i, int j) {
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || grid[i][j] == 0) {
            return;
        }
        grid[i][j] = 0;
        dfs(i + 1, j);
        dfs(i - 1, j);
        dfs(i, j + 1);
        dfs(i, j - 1);
    }
}
```

### [994.腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)
题解：

- 题意分析：腐烂的橘子会影响到上下左右四个方向的橘子，要求返回单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1
- 关键思想：从腐烂橘子影响的方式来看可以发现这是一个模拟广度优先遍历的过程。
   - 上下左右相邻的新鲜橘子就是该腐烂橘子尝试访问的同一层的节点，路径长度就是新鲜橘子被腐烂的时间。我们记录下每个新鲜橘子被腐烂的时间，最后如果单元格中没有新鲜橘子，腐烂所有新鲜橘子所必须经过的最小分钟数就是新鲜橘子被腐烂的时间的最大值。
   - 上述情况是图中只有一个已经腐烂的橘子，此题中**可能存在多个腐烂的橘子，就需要从多个起点开始广度优先遍历**
- 算法流程：
   - 初始阶段，将已有的腐烂橘子入队，具体是橘子的坐标$(x*n + y)$，新鲜橘子直接记录个数即可。
      - 小技巧：使用 `x * n + y`的方式简化坐标，可以直接保存一维即可。
   - 然后模拟广度优先搜索的过程：**当队列不为空时**，先弹出队首元素，然后将这个元素能够腐烂的橘子全部入队。
      - **每次 BFS 的过程为对上下左右四个方向的橘子进行腐烂**。
      - 广度优先搜索的次数就是需要花费的时间。
      - 注意这里遍历过程中可能会出现已经不存在新鲜橘子但还是进行了一次不做任何操作的循环，因此将判断条件扩充为     `!badOrange.isEmpty() && count1 > 0`
   - 最后的结果需要对新鲜橘子数量做个判断 `count1 == 0 ? time : - 1`
```java
class Solution {
    public int orangesRotting(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        // 1. 将橘子情况进行保存: 腐烂的橘子保存坐标, 新鲜橘子保存个数
        // 记录新鲜橘子个数
        int count1 = 0;
        // 记录全部坏橘子的坐标
        Queue<Integer> badOrange = new ArrayDeque<>();
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 2) {
                    badOrange.offer(i * n + j); // 简化坐标,本质上就是添加坐标(i,j)
                } else if (grid[i][j] == 1) {
                    count1++;
                }
            }
        }
        int time = 0;
        // 2. BFS
        // 当队列为空或者没有新鲜橘子了就停止循环
        while (!badOrange.isEmpty() && count1 > 0) {
            time++;
            int size = badOrange.size();
            for (int i = 0; i < size; i++) {
                int p = badOrange.poll();
                int x = p / n, y = p % n;
                // 上
                if (x - 1 >= 0 && grid[x - 1][y] == 1) {
                    count1--;
                    grid[x - 1][y] = 2;
                    badOrange.offer((x - 1) * n + y);
                }
                // 下
                if (x + 1 < m && grid[x + 1][y] == 1) {
                    count1--;
                    grid[x + 1][y] = 2;
                    badOrange.offer((x + 1) * n + y);
                }
                // 左
                if (y - 1 >= 0 && grid[x][y - 1] == 1) { 
                    count1--;
                    grid[x][y - 1] = 2;
                    badOrange.offer(x * n + y - 1);
                }
                // 右
                if (y + 1 < n && grid[x][y + 1] == 1) { 
                    count1--;
                    grid[x][y + 1] = 2;
                    badOrange.offer(x * n + y + 1);
                }
            }
        }
        // 3. 返回结果
        return count1 == 0 ? time : -1;
    }
}
```

### [207.课程表](https://leetcode.cn/problems/course-schedule/)
题解：

- 题意分析：要求在先修课程的约束下判断是否能完成所有课程的学习。这里 $prerequisities[i]=[a_i,b_i]$表示学习顺序为$b_i \rightarrow a_i$；从题意来看可以用**有向图抽象题目的数据模型**。
- 什么情况下不可能完成学习：由学习顺序和课程编号构成的图中不能存在环，要**判断图是否为有向无环图**——通过拓扑排序，拓扑排序完如果原图中还有节点未被访问到那么说明就存在环的。
- 拓扑排序需要什么：
   - 节点的入度情况——使用一个数组保存，下标是节点。值为入度值
   - 图中的节点情况，用于环的存在性判断——用邻接表结构
   - 一个用于 BFS 的队列
- 算法流程：
   - 数据结构选择：根据 $prerequisities[i]$构造邻接表，使用一维数组统计每个节点的入度值
   - 先构造好邻接表和统计各个节点的入度值
   - 然后进行拓扑排序（BFS）：
      - 初始化队列：先将入度为 0 的节点入队
      - BFS： 当队列非空时，依次将队首节点出队，同时在课程安排图中删除此节点（将此节点的邻接节点的入度值减一）；然后将删除后的入度为 0 的节点加入队列
      - 如何判断是否为有向无环图：如果不存在环所有节点应该都会在队列中出现过，那么可以记录下队列中出现过的节点个数，最后与原课程数做个判断即可。
```java
class Solution {
    // 邻接表
    List<List<Integer>> edges;
    // 节点入度值
    int[] indeg;

    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // 1. 初始化邻接表和节点入度值
        edges = new ArrayList<>();
        for (int i = 0; i < numCourses; ++i) {
            edges.add(new ArrayList<>());
        }
        indeg = new int[numCourses];
        // 构造邻接表以及入度表
        for (int[] info : prerequisites) {
            // 遵循题目要求的方向: bi -> ai
            edges.get(info[1]).add(info[0]);
            ++indeg[info[0]];
        }
        
        // 2. 进行拓扑排序,基于 BFS 实现
        Queue<Integer> queue = new ArrayDeque<>();
        // 保存入度为 0 的节点以用于开始拓扑排序
        for (int i = 0; i < numCourses; ++i) {
            if (indeg[i] == 0) {
                queue.offer(i);
            }
        }
    	// 队列中出现过的节点数
        int visited = 0;
        // 拓扑排序规则:队列中节点出队, 与其相连的节点入度减 1, 如果存在入度为 0 的节点,则加入队列
        while (!queue.isEmpty()) {
            ++visited;
            int u = queue.poll();
            for (int v : edges.get(u)) {
                --indeg[v];
                if (indeg[v] == 0) {
                    queue.offer(v);
                }
            }
        }
        // 3. 判断节点是否全部遍历
        return visited == numCourses;
    }
}
```

- 也可以直接利用深度优先遍历判断图中是否存在环
- 数据结构选择：根据 $prerequisities[i]$构造邻接表，使用一维标记数组来记录每个节点的状态：
   - 未被 DFS 访问：$flags[i] == 0$
   - 已被其他节点启动的 DFS 访问：$flags[i] == -1$
   - 已被当前节点启动的 DFS 访问：$flags[i] == 1$
- 从 flags 数组来判断是否存在环：当 DFS 到当前节点时状态值为 1，说明第二次访问了即存在环。
```java
class Solution {
    // 邻接表
    List<List<Integer>> edges;
    // 标记数组
    int[] flags;
    
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        edges = new ArrayList<>(numCourses);
        for (int i = 0; i < numCourses; i++) {
            edges.add(new ArrayList<>());
        }
        for (int[] info : prerequisites) {
            edges.get(info[1]).add(info[0]);
        }
        flags = new int[numCourses];
        for (int i = 0; i < numCourses; i++) {
            if (!dfs(i)) {
                return false;
            }
        }
        return true;
    }

    private boolean dfs(int i) {
        if (flags[i] == 1) {
            return false;
        }
        if (flags[i] == -1) {
            return true;
        }
        flags[i] = 1;
        for (Integer j : edges.get(i)) {
            if (!dfs(j)) {
                return false;
            }
        }
        flags[i] = -1;
        return true;
    }
}
```

- 同类型的题目：
   - [210. 课程表 II](https://leetcode.cn/problems/course-schedule-ii/description/?envType=daily-question&envId=2023-09-10)
   - [630. 课程表 III](https://leetcode.cn/problems/course-schedule-iii/)
   - [1462. 课程表 IV](https://leetcode.cn/problems/course-schedule-iv/?envType=daily-question&envId=2023-09-10)
210. 课程表 II题解：

- 题意分析：与上题的差别在于本题结果的一种正确的顺序即可
- 算法思想：只需要对 bfs 的过程进行改变：每次 bfs 都去添加上一个节点，最后返回结果即可
```java
class Solution {
    // 存储有向图
    List<List<Integer>> edges;
    // 存储每个节点的入度
    int[] indeg;
    // 存储答案
    int[] result;
    // 答案下标
    int index;

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        edges = new ArrayList<List<Integer>>();
        for (int i = 0; i < numCourses; ++i) {
            edges.add(new ArrayList<Integer>());
        }
        indeg = new int[numCourses];
        result = new int[numCourses];
        index = 0;
        for (int[] info : prerequisites) {
            edges.get(info[1]).add(info[0]);
            ++indeg[info[0]];
        }
        Queue<Integer> queue = new LinkedList<Integer>();
        // 将所有入度为 0 的节点放入队列中
        for (int i = 0; i < numCourses; ++i) {
            if (indeg[i] == 0) {
                queue.offer(i);
            }
        }

        while (!queue.isEmpty()) {
            // 从队首取出一个节点
            int u = queue.poll();
            // 放入答案中
            result[index++] = u;
            for (int v: edges.get(u)) {
                --indeg[v];
                // 如果相邻节点 v 的入度为 0，就可以选 v 对应的课程了
                if (indeg[v] == 0) {
                    queue.offer(v);
                }
            }
        }

        if (index != numCourses) {
            return new int[0];
        }
        return result;
    }
}
```
630. 课程表 III
1462. 课程表 IV

### [785.判断二分图](https://leetcode.cn/problems/is-graph-bipartite/)
题解：

### [542.01 矩阵](https://leetcode.cn/problems/01-matrix/)
题解：

### [127.单词接龙](https://leetcode.cn/problems/word-ladder/)
题解：

### [752.打开转盘锁](https://leetcode.cn/problems/open-the-lock/)
题解：

### [797.所有可能的路径](https://leetcode.cn/problems/all-paths-from-source-to-target/)
题解：

- 题意分析：给的是一个有向无环图，要求输出从开始节点到终点中所有的存在路径。题目中图是以邻接矩阵形式表示的。
- 算法思想：DFS
   - 使用两个集合，一个用于记录遍历过程，一个用于记录符合条件的答案。
   - 算法流程完全与回溯相同。
   - 三部曲：参数：当前节点编号；终止条件：当前节点已经到数组尾，收集结果，终止循环；循环：收集下一个节点，递归遍历。
```java
class Solution {
    int[][] graph;
    List<List<Integer>> ans;
    List<Integer> path;

    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        this.graph = graph;
        ans = new ArrayList<>();
        path = new ArrayList<>();
        // 加入起始节点
        path.add(0);
        dfs(0);
        return ans;
    }

    private void dfs(int node) {
        if (node == graph.length - 1) {
            ans.add(new ArrayList<>(path));
            return;
        }
        // graph[node] 为编号为 node 的节点的连接情况
        for (int i = 0; i < graph[node].length; i++) {
            int next = graph[node][i];
            path.add(next);
            dfs(next);
            path.remove(path.size() - 1);
        }
    }
}
```

### [399.除法求值](https://leetcode.cn/problems/evaluate-division/)
题解：

### [329.矩阵中的最长递增路径](https://leetcode.cn/problems/longest-increasing-path-in-a-matrix/)
题解：

### [LCR 114.火星词典](https://leetcode.cn/problems/Jf1JuT/)
题解：

### [LCR 115.序列重建](https://leetcode.cn/problems/ur2n8P/)
题解：

### [547.省份数量](https://leetcode.cn/problems/number-of-provinces/)
题解：

### [839.相似字符串组](https://leetcode.cn/problems/similar-string-groups/)
题解：

### [684.冗余连接](https://leetcode.cn/problems/redundant-connection/)
题解：


前缀树理论基础[https://leetcode.cn/problems/implement-magic-dictionary/solutions/1662444/by-lfool-pcng/](https://leetcode.cn/problems/implement-magic-dictionary/solutions/1662444/by-lfool-pcng/)
### [208.实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)
题解：

- 题意分析：$Trie$树（又叫「前缀树」或「字典树」）是一种用于快速查询「某个字符串/字符前缀」是否存在的数据结构。其核心是使用「边」来代表有无字符，使用「点」来记录是否为「单词结尾」以及「其后续字符串的字符是什么」。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40499251/1703507819832-fdc0bb4f-7f8e-4353-87dc-8bddfa92fccf.png#averageHue=%23e7ebf2&clientId=ubb8ac237-9fb7-4&from=paste&id=jdDWb&originHeight=583&originWidth=1024&originalType=url&ratio=1.25&rotation=0&showTitle=false&size=57501&status=done&style=none&taskId=ua5012377-c1ba-4bc4-9556-128e708a61c&title=)

- **思路的启发在于：字典树是一个棵树，寻找一个数据结构保存树中的节点，同时还要满足能表示出字符和指向子节点。**
```java
// 二叉树的节点定义, 节点本身保存值, 存在左右指针指向子节点
public class TreeNode {
    public int val;
    public TreeNode left;
    public TreeNode right;
}
```

- **设计思路：**
   - 数据结构选择：指向子节点的**指针数组**$children$，数组的长度就是需要存储内容的总字符集；布尔字段$isEnd$，表示该节点是否为字符串的结尾（为统一查找服务的）
- **插入字符串:**
   - 我们从字典树的根开始，插入字符串。对于当前字符对应的子节点，有两种情况：
      - 子节点存在。沿着指针移动到子节点，继续处理下一个字符。
      - 子节点不存在。创建一个新的子节点，记录在 $\textit{children}$ 数组的对应位置上，然后沿着指针移动到子节点，继续搜索下一个字符。
   - 重复以上步骤，直到处理字符串的最后一个字符，然后将当前节点标记为字符串的结尾。
- **查找前缀**：
   - 我们从字典树的根开始，查找前缀。对于当前字符对应的子节点，有两种情况：
      - 子节点存在。沿着指针移动到子节点，继续搜索下一个字符。
      - 子节点不存在。说明字典树中不包含该前缀，返回空指针。
   - 重复以上步骤，直到返回空指针或搜索完前缀的最后一个字符。
   - 若搜索到了前缀的末尾，就说明字典树中存在该前缀。此外，若前缀末尾对应节点的 $\textit{isEnd}$为真，则说明字典树中存在该字符串。
```java
class Trie {

    /**
     * 指向子节点的指针数组
     */
    private Trie[] children;

    /**
     * 判断当前节点是否为字符串的结尾
     */
    private boolean isEnd;
    
    public Trie() {
        children = new Trie[26];
        isEnd = false;
    }

    /**
     * 插入字符串
     *
     * @param word word
     */
    public void insert(String word) {
        // 字典树的根
        Trie node = this;
        for (int i = 0; i < word.length(); i++) {
            char ch = word.charAt(i);
            int index = ch - 'a';
            if (node.children[index] == null) {
                node.children[index] = new Trie();
            }
            node = node.children[index];
        }
        // 记录当前位置为尾部
        node.isEnd = true;
    }
    public boolean search(String word) {
        Trie node = searchPrefix(word);
        return node != null && node.isEnd;
    }

    public boolean startsWith(String prefix) {
        return searchPrefix(prefix) != null;
    }

    /**
     * 查找前缀
     *
     * @param prefix prefix
     * @return {@link Trie}
     */
    private Trie searchPrefix(String prefix) {
        Trie node = this;
        for (int i = 0; i < prefix.length(); i++) {
            char ch = prefix.charAt(i);
            int index = ch - 'a';
            if (node.children[index] == null) {
                return null;
            }
            node = node.children[index];
        }
        return node;
    }
}
```

- 题外话：事实上在工程中前缀树的用处不大，前缀匹配——哈希法就能解决大部分问题，模糊匹配、联想输入等等可以使用 ElasticSearch 完成。
- 同类型的题目：
   - [676.实现一个魔法字典](https://leetcode.cn/problems/implement-magic-dictionary/)
   - [648.单词替换](https://leetcode.cn/problems/replace-words/)
   - [211.添加与搜索单词 - 数据结构设计](https://leetcode.cn/problems/design-add-and-search-words-data-structure/)
   - [677.键值映射](https://leetcode.cn/problems/map-sum-pairs/)
   - [820.单词的压缩编码](https://leetcode.cn/problems/short-encoding-of-words/)
   - [421.数组中两个数的最大异或值](https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/)
   676.实现一个魔法字典
   648.单词替换
   211.添加与搜索单词 - 数据结构设计
677. 键值映射
820.单词的压缩编码
421.数组中两个数的最大异或值

# 回溯
### [46. 全排列](https://leetcode-cn.com/problems/permutations/)
题解：

- 收集结果的位置：在叶子结点收集，使用index标记深度
- 要求不同顺序的集合添加到结果中：**_for循环中的索引要从0开始_**——出现问题：元素重复选取；
- 使用used数组标记当前树层选择过的结点即可。
- 回溯三部曲：
   - 回溯函数参数：index--用于标记遍历深度
   - 收集结果的位置：到达叶子节点位置时更新结果
   - 遍历过程：因为不同顺序是不同结果，因此每次遍历从 0 开始，用 used 数组防止结果重复。
```java
class Solution {
    List<List<Integer>> ans;
    List<Integer> path;
    int[] nums;
    boolean[] used;

    public List<List<Integer>> permute(int[] nums) {
        this.nums = nums;
        ans = new ArrayList<>();
        path = new ArrayList<>(nums.length);
        used = new boolean[nums.length];
        dfs(0);
        return ans;
    }

    private void dfs(int index) {
        if (index == nums.length) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (!used[i]) {
                used[i] = true;
                path.add(nums[i]);
                dfs(index + 1);
                used[i] = false;
                path.remove(path.size() - 1);
            }
        }
    }
}
```

- 同类型的题目：
   - [47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)
   47.全排列 ||题解：

- 题意分析：与全排列的差别在于：
   - 原本的数组中出现重复数字，需要去重
```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    int[] nums;

    public List<List<Integer>> permuteUnique(int[] nums) {
        this.nums = nums;
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        dfs(0, used);
        return ans;
    }

    private void dfs(int index, boolean[] used) {
        if (index == nums.length) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            // 去重操作
            // 1. 因为数组已经被排序过, 那么判断当前元素和前一个元素大小关系即可
            // 2. 判断 used[i-1] 是为了保证是在同一树层上进行去重的
            if (i > 0 && nums[i] == nums[i - 1] && used[i - 1] == false) {
                continue;
            }
            if (!used[i]) {
                used[i] = true;
                path.add(nums[i]);
                dfs(index + 1, used);
                used[i] = false;
                path.remove(path.size() - 1);
            }
        }
    }
}
```

- 注：`used[i - 1] == false`说明已经回溯了，到了从 i 开始。

### [78.子集](https://leetcode.cn/problems/subsets/)
题解：

- 收集树上的全部结点，包括根节点。
- 同样要求不重复，因此要使用`startIndex`
- 回溯三问： 
   - 回溯函数参数：i --用于标记遍历起点
   - 收集结果位置：每次向下递归之前就统计一次结果。
   - 当前操作：枚举第i个元素选或者不选
```java
class Solution {
    List<List<Integer>> ans;
    List<Integer> path;
    int[] nums;

    public List<List<Integer>> subsets(int[] nums) {
        this.nums = nums;
        ans = new ArrayList<>();
        path = new ArrayList<>();
        dfs(0);
        return ans;
    }

    private void dfs(int startIndex) {
        ans.add(new ArrayList<>(path));
        if (startIndex == nums.length) {
            return;
        }
        for (int i = startIndex; i < nums.length; i++) {
            path.add(nums[i]);
            dfs(i + 1);
            path.remove(path.size() - 1);
        }
    }
}
```

- 同类型的题目：
   - [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)
90. 子集 ||题解：

- 题意分析：与子集不同点在于：原数组存在重复元素，因此需要去重操作
```java
class Solution {
    List<List<Integer>> ans;
    List<Integer> path;
    int[] nums;

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        this.nums = nums;
        ans = new ArrayList<>();
        path = new ArrayList<>();
        dfs(0);
        return ans;
    }

    private void dfs(int startIndex) {
        ans.add(new ArrayList<>(path));
        if (startIndex == nums.length) {
            return;
        }
        // 去重操作
        // 1. 因为数组已经被排序过, 那么判断当前元素和前一个元素大小关系即可
        // 2. 这里 i > startIndex 保证了是在同一层去去重的 
        for (int i = startIndex; i < nums.length; i++) {
            if (i > startIndex && nums[i - 1] == nums[i]) {
                continue;
            }
            path.add(nums[i]);
            dfs(i + 1);
            path.remove(path.size() - 1);
        }
    }
}
```

### [77.组合](https://leetcode.cn/problems/combinations/)
题解：

- 使用子集模板解题，注意剪枝 
   - 剪枝：需要选的数字个数：`d=k-path.size()`
   - 使用了一个小技巧：**从后向前遍历，简化for循环中的判断。**
```java
class Solution {
    private List<List<Integer>> ans = new ArrayList<>();
    private List<Integer> path = new ArrayList<>();
    private int n, k;

    public List<List<Integer>> combine(int n, int k) {
        this.n = n;
        this.k = k;
        dfs(n);
        return ans;
    }

    private void dfs(int i) {
        int d = k - path.size();
        if (d == 0) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int j = i; j >= d; j--) {
            path.add(j);
            dfs(j - 1);
            path.remove(path.size() - 1);
        }
    }
}
```

- 上述三题进行对比：
| 问题类型 | 收集结果位置 | 顺序要求 | 通用要求 |
| --- | --- | --- | --- |
| 排列问题 | 叶子结点位置 | 强调顺序，[0,1] 和 [1,0] 是两种情况 | 1.结果重复性（是否需要去重）
2.剪枝操作
3.需要回溯过程 |
| 子集问题 | 每个结点位置 | 不强调顺序 |  |
| 组合问题 | 指定位置 | 不强调顺序 |  |

后续回溯问题就弄清楚上述表格中的四个方面，配合 DFS 模板即可解题
```java
void dfs(参数) {
	if (终止条件) {
		收集结果;
		return;
	}
	for (选择：本层集合中元素) {
		处理结点;
		dfs(参数);
		回溯，撤销结果;
	}
}
```

### [17.电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)
题解：

- 题意分析：根据字母映射出的字符串组成所有可能存在的字母组合
- 分析问题:
   - 问题类型：组合问题；
   - 收集结果位置：当树层数为$digits.length$时收集结果
   - 顺序要求：无顺序要求
   - 结果不会重复，无剪枝操作，**注意下述代码中 path 用一维数组保存，因为默认初始化的存在，导致回溯操作隐藏**
```java
class Solution {
    private static final String[] MAPPING = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

    private final List<String> ans = new ArrayList<>();
    private char[] digits, path;

    public List<String> letterCombinations(String digits) {
        int n = digits.length();
        if (n == 0) return List.of();
        this.digits = digits.toCharArray();
        path = new char[n];
        dfs(0);
        return ans;
    }

    private void dfs(int i) {
        // 收集结果
        if (i == digits.length) {
            ans.add(new String(path));
            return;
        }
        // 横向遍历
        for (char c : MAPPING[digits[i] - '0'].toCharArray()) {
            path[i] = c;
            // 纵向递归
            dfs(i + 1);
        }
    }
}
```

### [39.组合总和](https://leetcode.cn/problems/combination-sum/)
题解：

- 题意分析：寻找数组中能组成 数组和等于 target 的组合，要注意每个数组元素可以重复选取。
- 分析问题：
   - 问题类型：组合问题
   - 收集结果位置：当当前和等于 target 时收集结果
   - 顺序要求：没有
   - 题目中说明数组中不存在重复元素，因此不会出现重复（与重复选取元素区分开）；剪枝操作：可以对数组先排序，这样当出现和大于 target 时就不需要继续搜索了；
```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    int[] candidates;
    int target;

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        this.candidates = candidates;
        this.target = target;
        Arrays.sort(candidates);
        dfs(0, 0);
        return ans;
    }

    private void dfs(int index, int sum) {
        if (sum == target) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int i = index; i < candidates.length; i++) {
            // 剪枝
            if (sum + candidates[i] > target) {
                break;
            }
            path.add(candidates[i]);
            // 元素可以重复使用
            dfs(i, sum + candidates[i]);
            path.remove(path.size() - 1);
        }
    }
}
```

- 同类型的题目：
   - [40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)
   - [216. 组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/)
   - [377. 组合总和 Ⅳ](https://leetcode.cn/problems/combination-sum-iv/)
   40.组合总和 ||题解：

- 题意分析：与组合总和的差别在于：
   - 1. 数组元素出现了重复
   - 2. 一个元素只能选一次
```java
class Solution {
    private List<List<Integer>> ans = new ArrayList<>();
    private List<Integer> path = new ArrayList<>();
    private int[] candidates;
    private int target;

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        this.candidates = candidates;
        this.target = target;
        Arrays.sort(candidates);
        dfs(0, 0);
        return ans;
    }

    private void dfs(int sum, int index) {
        if (sum == target) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int i = index; i < candidates.length; i++) {
            // 剪枝
            if (sum + candidates[i] > target) {
                break;
            }
            // 去重操作
            // 1. 因为数组已经被排序过, 那么判断当前元素和前一个元素大小关系即可
            // 2. 这里 i > index 保证了是在同一层去去重的
            if (i > index && candidates[i] == candidates[i - 1]) {
                continue;
            }
            path.add(candidates[i]);
            // i+1 保证不重复选取同一位置的元素
            dfs(sum + candidates[i], i + 1);
            path.remove(path.size() - 1);
        }
    }
}
```
216. 组合总和 III题解：

- 题意分析：和组合类似
```java
class Solution {
    private final List<List<Integer>> ans = new ArrayList<>();
    private final List<Integer> path = new ArrayList<>();
    private int k, n;

    public List<List<Integer>> combinationSum3(int k, int n) {
        this.k = k;
        this.n = n;
        dfs(1, 0);
        return ans;
    }

    private void dfs(int index, int sum) {
        if (sum == n && path.size() == k) {
            ans.add(new ArrayList<>(path));
            return;
        }
        if (sum > n) {
            return;
        }
        for (int i = index; i <= 9; i++) {
            path.add(i);
            dfs(i + 1, sum + i);
            path.remove(path.size() - 1);
        }
    }
}
```
377. 组合总和 Ⅳ题解：

- 题意与之前的基本相同，只是这里采用动态规划来解题。
   - 因为这里只需要返回和为 target 的总情况数即可。
- 整体思想就类似于爬楼梯：步数就是 nums 的值。楼层就是 target
- 算法流程：动态规划
   - 收集结果的位置是`dp[target]`
```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i - num >= 0) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}
```

### [22.括号生成](https://leetcode.cn/problems/generate-parentheses/)
题解：

- 题意分析：给 n 对括号，要求生成所有可能且有效的组合
   - 这里解释下有效：如果用一个栈保存左括号，右括号表示出栈的话，遍历有效的组合后栈是空的。
- 分析问题：
   - 问题的类型：组合问题
   - 收集结果的位置：path 的大小等于 $2n$
   - 顺序要求：没有要求
   - 结果重复性：不重复；没有剪枝操作；回溯操作：同样是 path 数组隐藏回溯
   - 遍历过程：枚举当前状态下可以选择的左括号数目和右括号数目
      - open 表示左括号数量，那么当前位置索引减去 open 就是右括号数量
```java
class Solution {
    List<String> ans = new ArrayList<>();
    char[] path; // path大小已知，因此直接使用char型数组
    int n;

    public List<String> generateParenthesis(int n) {
        this.n = n;
        path = new char[n * 2];
        dfs(0, 0);
        return ans;
    }

    private void dfs(int i, int open) {
        if (i == n * 2) {
            ans.add(new String(path));
            return;
        }
        // 选：添加左括号
        if (open < n) {
            path[i] = '(';
            dfs(i + 1, open + 1);
        }
        // 不选：不选添加右括号
        if (i - open < open) {
            path[i] = ')';
            dfs(i + 1, open);
        }
    } 
}
```

- 同类型的题目：
   - [301. 删除无效的括号](https://leetcode.cn/problems/remove-invalid-parentheses/)
301. 删除无效的括号题解：

```java
class Solution {
    List<String> ans;
    Set<String> set = new HashSet<>();
    int max, len;
    String s;

    public List<String> removeInvalidParentheses(String s) {
        this.s = s;
        int lcount = 0, rcount = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                lcount++;
            } else if (c == ')') {
                rcount++;
            }
        }
        max = Math.min(lcount, rcount);
        dfs(0, "", 0);
        return new ArrayList<>(set);
    }

    private void dfs(int index, String path, int score) {
        if (score < 0 || score > max) {
            return;
        }
        if (index == s.length()) {
            if (score == 0 && path.length() >= len) {
                if (path.length() > len) {
                    set.clear();
                }
                len = path.length();
                set.add(path);
            }
            return;
        }
        char c = s.charAt(index);
        if (c == '(') {
            dfs(index + 1, path + String.valueOf(c), score + 1);
            dfs(index + 1, path, score);
        } else if (c == ')') {
            dfs(index + 1, path + String.valueOf(c), score - 1);
            dfs(index + 1, path, score);
        } else {
            dfs(index + 1, path + String.valueOf(c), score);
        }
    }
}
```

### [79.单词搜索](https://leetcode.cn/problems/word-search/)
题解：

- 题意分析：
- 问题分析：典型的回溯法爆搜题，直接套 DFS 模板即可
```java
class Solution {
    private boolean ans = false;
    private char[] chs;
    private char[][] board;
    private int m, n;
    
    public boolean exist(char[][] board, String word) {
        this.board = board;
        m = board.length;
        n = board[0].length;
        chs = word.toCharArray();
        boolean[][] used = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == chs[0]) {
                    dfs(i, j, used, 0);
                }
            }
        }
        return ans;
    }

    private void dfs(int x, int y, boolean[][] used, int index) {
        if (ans) {
            return;
        }
        if (index == chs.length) {
            ans = true;
            return;
        }
        if (x < 0 || x >= m || y < 0 || y >= n || board[x][y] != chs[index]) {
            return;
        }
        if (used[x][y]) {
            return;
        }
        used[x][y] = true;
        dfs(x + 1, y, used, index + 1);
        dfs(x - 1, y, used, index + 1);
        dfs(x, y + 1, used, index + 1);
        dfs(x, y - 1, used, index + 1);
        used[x][y] = false;
    }
}
```

### [131.分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)
题解：

- 题意分析：将一个字符串分割成多个回文子串，返回所有的分割方案；
- 换个思路看问题：给定一个字符数组，求其中的子集，要求子集中的字符能组成回文串
- 分析问题：
   - 问题类型：子集问题
```java
class Solution {
    private final List<List<String>> ans = new ArrayList<>();
    private final List<String> path = new ArrayList<>();
    private String s;

    public List<List<String>> partition(String s) {
        this.s = s;
        dfs(0);
        return ans;
    }

    private void dfs(int i) {
        if (i == s.length()) {
            ans.add(new ArrayList<>(path));
            return;
        }
        for (int j = i; j < s.length(); j++) {
            if (isPalindrome(i, j)) {
                path.add(s.substring(i, j + 1));
                dfs(j + 1);
                path.remove(path.size() - 1);
            }
        }
    }

    private boolean isPalindrome(int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) {
                return false;
            }
        }
        return true;
    }
}
```


### [93.复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)
题解：

- 分割问题，难点在于拼接
- 转换问题成在原字符串上添加点，**_点本质上就是分割线。_**
- 递归终止情况：三个点打完。收集结果之前还需要判断下最后一段是否合法；
- 回溯函数中的操作就是添加点；
- 判断合法性：二种情况不合法：前导零；超过255
```java
class Solution {
    List<String> res = new ArrayList<>();

    public List<String> restoreIpAddresses(String s) {
        StringBuilder sb = new StringBuilder(s);
        backtracking(sb, 0, 0);
        return res;
    }

    private void backtracking(StringBuilder s, int startIndex, int pointNum) {
        // 收集结果
        if (pointNum == 3) {
            // 判断下第四段是否合法
            if (isValid(s, startIndex, s.length() - 1)) {
                res.add(s.toString());
            }
            return;
        }
        
        for (int i = startIndex; i < s.length(); i++) {
            if (isValid(s, startIndex, i)) {
                s.insert(i + 1, '.');
                backtracking(s, i + 2, pointNum + 1);
                s.deleteCharAt(i + 1);
            } else {
                break;
            }
        }
    }

    // 判断 IP 地址合法性
    private boolean isValid(StringBuilder s, int left, int right) {
        if (left > right) {
            return false;
        }
        // 不能含前导零
        if (s.charAt(left) == '0' && left != right) {
            return false;
        }
        // 判断数字范围
        int num = 0;
        for(int i = left; i <= right; i++){
            int digit = s.charAt(i) - '0';
            num = num * 10 + digit;
            if(num > 255)
                return false;
        }
        return true;
    }
}
```

### [51.N 皇后](https://leetcode.cn/problems/n-queens/)
题解：

- 题意分析：N 皇后问题，棋盘默认是`'.'`标记，要求给每行上填入一个皇后`'Q'`满足棋盘上每行每列每个对角上只有一个`'Q'`
- 算法思想：回溯爆搜，
   - 回溯三部曲：
      - 回溯函数参数：行数和棋盘。
      - 收集结果位置：当遍历完所有行后就收集结果。也就是`row == n`
      - 遍历过程：遍历当前行的每一列，判断当前位置能不能放置皇后。
   - 判断函数：判断当前的位置的行列对角存不存在其他皇后，需要注意回溯爆搜是从上往下搜的，也就是说在判断当前位置是否合法时，下面的部分还未填充皇后因此不需要进行判断。
```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    int n;

    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        // ch模拟棋盘
        char[][] ch = new char[n][n];
        for (char[] c : ch) {
            Arrays.fill(c, '.');
        }
        dfs(0, ch);
        return res;
    }

    private void dfs(int row, char[][] ch) {
        // 叶子结点收集结果
        if (row == n) {
            // res.add(arrayToList(ch));
            List<String> temp = Arrays.stream(ch).map(String::copyValueOf).toList();
            res.add(temp);
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isValid(row, col, ch, n)) {
                // 合法位置放皇后
                ch[row][col] = 'Q';
                dfs(row + 1, ch);
                // 回溯
                ch[row][col] = '.';
            }
        }
    }

    // 判断皇后放置位置是否合法
    private boolean isValid(int row, int col, char[][] ch, int n) {
        // 检查行, 回溯爆搜时给每一行就设置一个皇后,保证行不重复
        
        // 检查列
        for (int i = 0; i < row; i++) { // 这是一个剪枝
            if (ch[i][col] == 'Q') {
                return false;
            }
        }
        // 检查 45 度角是否有皇后
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if (ch[i][j] == 'Q') {
                return false;
            }
        }
        // 检查 135 度角是否有皇后
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
            if (ch[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }

    // 将二维数组转换成字符串集合
    private List arrayToList(char[][] ch) {
        List<String> list = new ArrayList<>();
        for (char[] c : ch) {
            list.add(String.copyValueOf(c));
        }
        return list;
    }
}
```

- 同类型的题目：
   - [52. N 皇后 II](https://leetcode.cn/problems/n-queens-ii/)
   - [37. 解数独](https://leetcode-cn.com/problems/sudoku-solver/)
52. N 皇后 II题解：

- 与上题基本无差别，主要在回溯函数收集结果的位置。
37. 解数独题解：

- 要求给数独中的空格填数，数字范围为 1-9；
- 基本思路和 N 皇后类似。
- 判断函数的边界统一为左必右开。
```java
class Solution {
    public void solveSudoku(char[][] board) {
        solveSudokuHelper(board);
    }

    private boolean solveSudokuHelper(char[][] board){
        //「一个for循环遍历棋盘的行，一个for循环遍历棋盘的列，
        // 一行一列确定下来之后，递归遍历这个位置放9个数字的可能性！」
        for (int i = 0; i < 9; i++){ // 遍历行
            for (int j = 0; j < 9; j++){ // 遍历列
                if (board[i][j] != '.'){ // 跳过原始数字
                    continue;
                }
                for (char k = '1'; k <= '9'; k++){ // (i, j) 这个位置放k是否合适
                    if (isValidSudoku(i, j, k, board)){
                        board[i][j] = k;
                        if (solveSudokuHelper(board)){ // 如果找到合适一组立刻返回
                            return true;
                        }
                        board[i][j] = '.';
                    }
                }
                // 9个数都试完了，都不行，那么就返回false
                return false;
                // 因为如果一行一列确定下来了，这里尝试了9个数都不行，说明这个棋盘找不到解决数独问题的解！
                // 那么会直接返回， 「这也就是为什么没有终止条件也不会永远填不满棋盘而无限递归下去！」
            }
        }
        // 遍历完没有返回false，说明找到了合适棋盘位置了
        return true;
    }


    private boolean isValidSudoku(int row, int col, char val, char[][] board) {
        // 同行是否重复
        for (int i = 0; i < 9; i++){
            if (board[row][i] == val){
                return false;
            }
        }
        // 同列是否重复
        for (int j = 0; j < 9; j++){
            if (board[j][col] == val){
                return false;
            }
        }
        // 9宫格里是否重复
        int startRow = (row / 3) * 3;
        int startCol = (col / 3) * 3;
        for (int i = startRow; i < startRow + 3; i++){
            for (int j = startCol; j < startCol + 3; j++){
                if (board[i][j] == val){
                    return false;
                }
            }
        }
        return true;
    }
}
```

# 二分查找
拓展资料：[二分算法](https://www.yuque.com/northmaples/waiting/dktz4ngyncfsz6k2#b85aa960)

### [35.搜索插入位置](https://leetcode.cn/problems/search-insert-position/)
题解：

- 题意分析：给定一个目标值，搜索已排序的数组中是否存在该值，如果存在返回下标，如果不存在则插入合适的位置（插入后数组还是有序的）
- 算法思想：二分查找
   - 最终的答案范围：$[0, n - 1]$
   - check 函数：
   - 循环不变量：
   - 返回的结果要求：
```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        // 答案区间 [0, n - 1]
        int left = -1, right = nums.length;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            // check 函数
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                right = mid;  // (left, mid) 一定小
            } else {
                left = mid;  // (mid, right) 一定大
            }
        }
        // 返回大区间的小值
        return right;
    }
}
```

### [852.山脉数组的峰顶索引](https://leetcode.cn/problems/peak-index-in-a-mountain-array/)
题解：

- 题意分析：给定一个长度为 `n` 的整数 山脉 数组 `arr` ，其中的值递增到一个峰值元素然后递减。返回峰值元素的下标。
- 思路一：**山形数组的通法：构造前后缀数组**
   - 时间复杂度不符合要求。
- 思路一的缺陷：根据题意可以知道，这里的山形数组还有额外的条件：山顶的左侧单调递增，山顶的右侧单调递减——二分查找
- 思路二：二分查找来查找山顶——山顶左边单调增，右边单调减
   - 答案区间：$[1, n - 2]$，山形数组的峰顶会默认左右存在元素且小于自身。
   - check 函数：判断当前位置是否在递增区间或者递减区间内。
   - 循环不变量：左边一定增，右边一定减。
   - 返回的结果情况：题目默认一定存在峰顶元素，直接循环内部返回。
```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        int n = arr.length;
        // 答案区间: [1, n - 2]
        int left = 0, right = n - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (arr[mid - 1] > arr[mid] && arr[mid] > arr[mid + 1]) {
                right = mid;     // (left, mid) 一定单调增
            } else if (arr[mid - 1] < arr[mid] && arr[mid] < arr[mid + 1]) {
                left = mid;    // (mid, right) 一定单调减
            } else {
                return mid;
            }
        }
        // 一定存在山顶元素, 返回 left/right 都行
        return right;
    }
}
```

### [540.有序数组中的单一元素](https://leetcode.cn/problems/single-element-in-a-sorted-array/)
题解：

- 题意分析：一个仅由整数组成的有序数组，其中每个元素都会出现两次，唯有一个数只会出现一次。请你找出并返回只出现一次的那个数。
- 思路一：位运算，利用异或计算的性质（见 [136.只出现一次的数字](https://leetcode.cn/problems/single-number/)）
   - 原理很简单：**相同的元素异或等于 0，其他数与 0 异或为其本身。**
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int n = nums.length;
        int ans = nums[0];
        for (int i = 1; i < n; i++) {
            ans ^= nums[i];
        }
        return ans;
    }
}
```

- 上述思路没达到题目的复杂度要求：$O(log(n))$
- 进一步对题意进行分析：**数组中只出现一次的元素的下标一定是偶数，而其他元素无论是奇数还是偶数情况都会与靠近的元素(靠近元素下标：**`**index ^ 1**`**)相等。**
- 关键点：对于二分答案的分析：
   - mid 为偶数下标：根据上述结论，正常情况下偶数下标的值会与下一值相同，翻译成代码就是 `index % 2 == 0 -> nums[index+1] = nums[index] `
      - **因此如果满足该条件，可以确保 mid 之前并没有插入单一元素。**正常情况下，此时应该更新 l=mid，否则应当让 r=mid−1，但需要注意这样的更新逻辑，会因为更新 r时否决 mid 而错过答案，我们可以将否决 mid 的动作放到更新 l 的一侧，即需要将更新逻辑修改为 l=mid+1 和 r=mid；
      - 演示下错过答案的情况：
   - mid 为奇数下标：同理，根据上述结论，正常情况下奇数下标的值会与上一值相同，
      - 因此如果满足该条件，可以确保 mid 之前并没有插入单一元素，还是通过相应的方式更新 l 和 r。
   - 为了简化判断，将两种情况合并，如果 mid 为奇数，就判断将 mid--，变为偶数，这样两种情况下都统一成了判断当前位置和后一个位置。
- 思路二：二分答案
   - 答案区间：
   - check 函数
   - 循环不变量
   - 返回的结果情况 
- **NOTE：位运算(**`**num ^ 1**`**)可以统一当前位置奇偶性的判断。**
```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int n = nums.length;
        // 答案区间: [0, n - 1]
        int left = -1, right = n;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            // 确保 mid 是偶数
            if (mid % 2 == 1) {
                mid--;
            }
            if (mid + 1 < n && nums[mid] == nums[mid + 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return nums[right];
    }

    // 基于位运算优化
    public int singleNonDuplicate(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == nums[mid ^ 1]) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return nums[right];
    }
}
```

### [528.按权重随机选择](https://leetcode.cn/problems/random-pick-with-weight/)
题解：

- 题意分析：给定一个权重数组$w[i]$，值越大$i$被选中的概率就越大。要求实现一个随机返回下标的函数.
   - 函数要求：随机地从范围$[0, w.length - 1]$ 内（含 0 和 w.length - 1）选出并返回一个下标。选取下标 i 的 概率 为 $w[i] / sum(w)$
- 再仔细分析下这个选取策略：$sum$是固定的，而$w[i]$决定了是否可能被选举。
- 注意这里虽然说随机，但选取的概率是固定的。
```java
class Solution {
    // 前缀和数组
    int[] sum;

    public Solution(int[] w) {
        int n = w.length;
        sum = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            sum[i] = sum[i - 1] + w[i - 1];
        }
    }

    public int pickIndex() {
        int n = sum.length;
        int target = (int) (Math.random() * sum[n - 1]) + 1;
        // 答案区间 [0, n - 1]
        int left = -1, right = n;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (sum[mid] >= target) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return right - 1;
    }
}
```

- 拓展知识：[https://pages.cs.wisc.edu/~remzi/OSTEP/Chinese/09.pdf](https://pages.cs.wisc.edu/~remzi/OSTEP/Chinese/09.pdf)

### [69.x 的平方根](https://leetcode.cn/problems/sqrtx/)
题解：

- 题意分析：不使用库函数，实现对$x$的开方，要求只返回整数部分，如果存在小数部分，直接舍去。
- 思路一：二分查找指定区间
   - 答案区间：$[0, x]$
   - check 函数：设返回的数为$k$，满足$k^2 \le x$，要求的就是满足条件的最大 k。
   - 循环不变量：区间默认单调递增。
   - 返回的结果情况：循环过程中去记录可能的值。
```java
class Solution {
    public int mySqrt(int x) {
        int ans = -1;
        // 答案区间: [0, x]
        int left = 0, right = x;   // 使用开区间会遇到爆 int 的问题
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if ((long) mid * mid <= x) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```

- 思路二：偷个懒，转为指数和对数进行计算，也就是$e^{\frac{1}{2} logx}$
   - 注意处理：`(long) (ans + 1) * (ans + 1) <= x ? ans + 1 : ans;`
```java
class Solution {
    public int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        int ans = (int) Math.exp(0.5 * Math.log(x));
        return (long) (ans + 1) * (ans + 1) <= x ? ans + 1 : ans;
    }
}
```

### [875.爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)
题解：

- 题意分析：要求返回 h 小时内吃掉所有香蕉的**最小速度**
   - $piles$数组：下标 i 是堆的索引，值是香蕉个数
   - 吃香蕉的规则：即使香蕉个数小于吃的速度，也会花费一个小时，同时不会去吃其他堆的香蕉。（计算时间时要向上取整）
- 问题：怎么算出示例给的结果的？
   - 吃每堆香蕉花费的时间$\lceil \frac{piles[i]}{k} \rceil$
   - 可以得出这样的等式$\sum_{i=0}^{n-1}{\lceil \frac{piles[i]}{k} \rceil} = h$,求的是 $k_{min}$
- 思路：使用二分查找猜答案：在合适的区间内用二分法去检索最小的 k
   - 单调性：珂珂吃香蕉的速度越小，耗时越多。反之，速度越大，耗时越少
   - 合适的区间：区间最大值：显然就是最多香蕉的堆，区间最小值：1
   - 染色规则：花的时间超过了 h，说明猜小了。
   - 需要注意的是：当「二分查找」算法猜测的速度恰好使得珂珂在规定的时间内吃完香蕉的时候，还应该去尝试更小的速度是不是还可以保证在规定的时间内吃完香蕉。
- 算法流程：
   - 答案区间：$[1, max - 1]$
   - check 函数：判断当前速度是否太快
   - 循环不变量：右边速度一定大，左边速度一定小。
   - 返回的结果情况：k 不能太小，但要选最小值。
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int max = 0;
        for (int x : piles) {
            max = Math.max(max, x);
        }
        // 答案区间:[1, max]
        int left = 0, right = max + 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (check(piles, mid, h)) {
                right = mid;
            } else {
                left = mid;
            }
        }
        // 要求的是 speed_min
        return right;
    }

    private boolean check(int[] piles, int speed, int h) {
        int time = 0;
        for (int x : piles) {
            // 计算单个堆时间规则是向上取整
            time += (x + speed - 1) / speed;
            if (time > h) {
                // 速度太慢了
                return false;
            }
        }
        return true;
    }
}
```

### [74. 搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)
题解：

- 题意分析：在一个已排序好的二维矩阵中查找目标值。
   - 二维矩阵的排序情况：从左到右单调增；从上到下单调增；
- 对于矩阵的进一步分析：如果将矩阵每一个行进行拼接，那么本质上这个矩阵就是一个单调递增数组。
- 算法思路：只需要解决掉二维矩阵元素坐标映射到一维数组中的位置情况，就可以直接使用二分思想来完成查找。
   - 映射规则--一维坐标映射为二维坐标：$x \rightarrow i = x / n, j = x \% n$
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].length;
        // 答案区间: [0, m * n - 1]
        int left = -1, right = m * n;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            // 一维坐标映射为二维坐标
            int x = mid / n;
            int y = mid % n;
            if (matrix[x][y] > target) {
                right = mid;
            } else if (matrix[x][y] < target) {
                left = mid;
            } else {
                return true;
            }
        }
        return false;
    }
}
```

### [34.在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)
题解：

- 题意分析：给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。
- 难点：
   - **数组非递减顺序排列，那么就存在元素重复，那么需要设计好 check 函数，最好查的就是元素刚出现的位置。**
   - **要求返回的时开始和结束两个位置，根据上一点分析，这需要确定两个不同的 target，那么结果就是两个查找位置构成的子数组。**
- 基本思想：两次二分查找
   - 第一次二分查找去查找目标值第一次出现的地方；
   - 因为非递减的性质，我们可以第二次二分查找的时候去查找$target+1$元素的位置，这会得到目标值最后一次出现的后一个位置。
      - 需要注意，即使 $target+1$不存在，也可以确定到正确的位置上。
- 下面解决最后一个问题：**如何在非递减排列数组中如何查找目标值第一次出现的位置？**
- 仔细思考下：**什么叫第一次出现：**
   - $左 < index \le 右$**，当前位置的值一定不与左边相同，而可能等于或小于右边。**
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int left = lowerBound(nums, target);
        if (left == nums.length || nums[left] != target) {
            return new int[]{-1, -1};
        }
        int right = lowerBound(nums, target + 1);
        return new int[]{left, right - 1};
    }

    private int lowerBound(int[] nums, int target) {
        // 答案区间:[0, n - 1]
        int left = -1, right = nums.length;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid; // 左边小于
            } else {
                right = mid; // 右边大于等于
            }
        }
        // 找 大于等于 target 的最小值
        return right;
    }
}
```

### [33.搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)
题解：

- 题意分析：在一个旋转排序数组中查找指定元素；
   - 旋转排序数组：原本有序的数组的一部分（头/尾部）移动到尾/头部。
   - 数组是单调增的，分割点处的左右区间本身都是单调增的。
> 旋转排序数组和山脉数组的区别：
> - 山脉数组山峰的位置左右相对自身都是单调的；
> - 而旋转排序数组不同，连接点处则相对自身并没有规律，而是分割开的左右半区间有单调性。
> 
两者的区别会影响 check 函数的确定！

- **思路启发点：**
   - **将数组一分为二，其中一定有一个是有序的，另一个可能是有序，也能是部分有序。**
   - **此时如果判断出来当前位置处于有序部分就可以用二分法查找。**
   - **而如果处于无序部分就再一分为二，其中一个一定有序，另一个可能有序，可能无序。**
   - **继续就这样循环，直到查找到目标值或者说循环结束返回-1。**
- 注：所有分割的有序子数组都是单调递增的。
- 需要注意的是这里判断出来的左边有序是不等于$[left,mid]$是有序的，因此还需要条件$target \geq nums[left]$或者$nums[right] \geq target$来判断是否是继续二分查找还是说换到另一个区间进行进一步的划分。
```java
class Solution {
    public int search(int[] nums, int target) {
        int n = nums.length;
        // 答案区间: [0, n - 1]
        int left = 0, right = n - 1;    // 使用闭区间以简化后续的二分查找过程
        if (n == 1) {
            return target == nums[0] ? 0 : -1;
        }
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            // 左边有序
            if (nums[left] <= nums[mid]) {
                // 二分查目标值
                if (nums[mid] > target && nums[left] <= target) {
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else { // 右边有序
                // 二分查目标值
                if (nums[mid] < target && nums[right] >= target) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
```

- 同类型的题目：
   - [81.搜索旋转排序数组 II](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/)
   - [153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)
   - [154.寻找旋转排序数组中的最小值 II](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)
   81.搜索旋转排序数组 II题解：

- 题意分析：与上题的差别在于本题的数组中可能存在重复元素
- 直接用上题的方法出现错误：问题出在当$nums[left]==nums[mid]==nums[right]$的时候之前判断有序的方法失效。
- 进一步细分判断左右子数组有序性：单独处理上述情况，如果三者相等，那么就重新更新边界再继续判断。
```java
class Solution {
    public boolean search(int[] nums, int target) {
        int n = nums.length;
        if (n == 0) {
            return false;
        }
        if (n == 1) {
            return nums[0] == target;
        }
        int l = 0, r = n - 1;
        while (l <= r) {
            int mid = (l + r) / 2;
            if (nums[mid] == target) {
                return true;
            }
            if (nums[l] == nums[mid] && nums[mid] == nums[r]) {
                ++l;
                --r;
            } else if (nums[l] <= nums[mid]) {
                if (nums[l] <= target && target < nums[mid]) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            } else {
                if (nums[mid] < target && target <= nums[n - 1]) {
                    l = mid + 1;
                } else {
                    r = mid - 1;
                }
            }
        }
        return false;
    }
}
```

- 注意这题的最差的时间复杂度是$O(n)$
   - 当数组元素都相等且不等于 target 的时候复杂度最高。
153. 寻找旋转排序数组中的最小值题解：

- 题意分析：在旋转排序数组中查找最小值。
- 算法思想于之前提到的思路相同。
```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        // 答案区间:[0, n - 1]
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] <= nums[n - 1]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return nums[left];
    }
}
```
154.寻找旋转排序数组中的最小值 II题解：

- 题意分析：与 153 的差别在于旋转排序数组中存在重复的元素：
- 额外的处理：
```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        int left = 0, right = n - 2;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < nums[right + 1]) {
                right = mid - 1;
            } else if (nums[mid] > nums[right + 1]){
                left = mid + 1;
            } else {
                right--;
            }
        }
        return nums[left];
    }
}
```

### [4.寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)
题解：

- 题意分析：给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。找出并返回这两个正序数组的中位数（如果数组长度为偶数则返回均值）。
- 具体解题思路：[中位数的小技巧](https://leetcode.cn/problems/median-of-two-sorted-arrays/solutions/3983/shuang-zhi-zhen-by-powcai/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int n1 = nums1.length;
        int n2 = nums2.length;
        if (n1 > n2)
            return findMedianSortedArrays(nums2, nums1);
        int k = (n1 + n2 + 1) / 2;
        int left = 0;
        int right = n1;
        while (left < right) {
            int m1 = left + (right - left) / 2;
            int m2 = k - m1;
            if (nums1[m1] < nums2[m2 - 1])
                left = m1 + 1;
            else
                right = m1;
        }
        int m1 = left;
        int m2 = k - left;
        int c1 = Math.max(m1 <= 0 ? Integer.MIN_VALUE : nums1[m1 - 1],
                m2 <= 0 ? Integer.MIN_VALUE : nums2[m2 - 1]);
        if ((n1 + n2) % 2 == 1)
            return c1;
        int c2 = Math.min(m1 >= n1 ? Integer.MAX_VALUE : nums1[m1],
                m2 >= n2 ? Integer.MAX_VALUE : nums2[m2]);
        return (c1 + c2) * 0.5;
    }
}
```

# 枚举
### [926.将字符串翻转到单调递增](https://leetcode.cn/problems/flip-string-to-monotone-increasing/)
题解：

- 题意分析：目标是将原字符串中 0/1 翻转使得最终的字符串单调递增，求最小的翻转次数
- 最终字符串的可能存在形式固定，只可能是$00...00, 00...11, 11...11$这三种，可以尝试枚举每个位置上将左边的$1$变为$0$加上右边的$0$变为$1$的总次数
- 思路：通过前缀和保存 1 的次数
   - 每次更新结果时，计算当前位置左边 1 的个数：前一个位置前缀和的值；计算当前位置右边 0 的个数：末尾前缀和减当前位置前缀和的值为 1 的个数，总长度减去 1 的个数就是 0 的个数
```java
class Solution {
    public int minFlipsMonoIncr(String s) {
        char[] chs = s.toCharArray();
        int n = s.length();
        int[] preSum = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            preSum[i] = preSum[i - 1] + chs[i - 1] - '0'; 
        }
        int ans = n;
        for (int i = 1; i <= n; i++) {
            int left = preSum[i - 1];
            int right = (n - i) - (preSum[n] - preSum[i]);
            ans = Math.min(ans, left + right);
        }
        return ans;
    }
}
```

# 栈
### [150.逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)
题解：

- 题意分析：根据逆波兰表达式求值
- 思路：模拟， 用栈保存操作数，根据操作符进行计算即可。
   - 给的已经是一个逆波兰表达式，不需要栈空判断
```java
class Solution {
    public int evalRPN(String[] tokens) {
        Deque<Integer> stack = new ArrayDeque<>();
        // 计算规则是栈顶的下一个元素在符号左侧, 栈顶元素在符号右侧
        for (String token : tokens) {
            if ("+".equals(token)) {
                stack.push(stack.pop() + stack.pop());
            } else if ("-".equals(token)) {
                stack.push(-stack.pop() + stack.pop());
            } else if ("*".equals(token)) {
                stack.push(stack.pop() * stack.pop());
            } else if ("/".equals(token)) {
                int right = stack.pop();
                int left = stack.pop();
                stack.push(left / right);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }
}
```

### [735.小行星碰撞](https://leetcode.cn/problems/asteroid-collision/)
题解：

- 题意分析：给定一个整数数组 `asteroids`，表示在同一行的小行星，找出碰撞后剩下的所有小行星。
   - 移动规则：正数表示向右移动，负数表示向左移动。
   - 碰撞规则：
      - 正数和负数才会碰撞；
      - 绝对值大的吃绝对值小的，大的不变，小的直接消失；如果相等两者都消失。
- 算法思想：模拟+栈
   - 用一个栈保存正数
```java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        // 保存行星值
        Deque<Integer> stack = new ArrayDeque<>();
        for (int x : asteroids) {
            // 判断当前行星在处理后还存不存在
            boolean alive = true;
            while (alive && x < 0 && !stack.isEmpty() && stack.peek() > 0) {
                alive = stack.peek() < -x;
                // 栈顶爆炸情况
                if (stack.peek() <= -x) {
                    stack.pop();
                }
            }
            if (alive) {
                stack.push(x);
            }
        }
        int n = stack.size();
        int[] ans = new int[n];
        // 返回的结果需要保持原数组中的相对顺序, 因此需要倒序遍历
        for (int i = n - 1; i >= 0; i--) {
            ans[i] = stack.pop();
        }
        return ans;
    }
}
```

### [20.有效的括号](https://leetcode.cn/problems/valid-parentheses/)
题解：

- 题意分析：括号匹配问题
- 使用栈：这里的栈**存放的是待匹配的括号**（也就是如果遍历到`(`，则待匹配一个`)`，则将`)`入栈），最后判断栈是否为空即可。
- 情况分类：
   - 左括号情况：说明需要右括号匹配，右括号入栈；
   - 右括号情况：说明正在匹配，又分为两种情况：
      - 匹配失败：栈为空，或者是不对应，直接返回 false；
      - 匹配成功：出栈。
```java
class Solution {
    public boolean isValid(String s) {
        char[] chs = s.toCharArray();
        Deque<Character> stack = new ArrayDeque<>();
        for (int i = 0; i < s.length(); i++) {
            if (chs[i] == '(') {
                stack.push(')');
            } else if (chs[i] == '{') {
                stack.push('}');
            } else if (chs[i] == '[') {
                stack.push(']');
            } else if (stack.isEmpty() || stack.peek() != chs[i]) {
                return false;
            } else {
                stack.pop();
            }
        }
        return stack.isEmpty();
    }
}
```

- 同类型的题目：
   - [1003. 检查替换后的词是否有效](https://leetcode.cn/problems/check-if-word-is-valid-after-substitutions/)
1003. 检查替换后的词是否有效题解：

- 题意分析：给你一个字符串 s ，请你判断它是否有效 。
   - 字符串 s 有效需要满足：假设开始有一个空字符串 t = "" ，你可以执行任意次下述操作将 t 转换为 s ：
   - 将字符串 "abc" 插入到 t 中的任意位置。形式上，t 变为 $t_{left} + "abc" + t_{right}$，其中 $t == t_{left} + t_{right}$。注意，$t_{left}$ 和 $t_{right}$ 可能为 空 。
   - 如果字符串 s 有效，则返回 true；否则，返回 false。
- 如果题目是根据插入 ab 来检查是否有效，问题就完全转换成了上题，现在是 abc 三个字符，就需要增加一组规则（**实际上不需要增加规则，而是做两两匹配，也就是分 ab，bc 两类字符串**）：
   - 遍历到字符 a：相当于遍历到左括号，直接入栈
   - 遍历到字符 b：如果栈空或者栈不为 a，说明匹配失败，否则更新栈顶元素为 b
   - 遍历到字符 c：如果栈空或者栈不为 b， 说明匹配失败，否则相当于遍历到右括号，直接出栈
```java
class Solution {
    public boolean isValid(String s) {
        char[] chs = s.toCharArray();
        Deque<Character> stack = new ArrayDeque<>();
        for (char ch : chs) {
            // 相当于左括号
            if (ch == 'a') {
                stack.push('a');
            } else if (ch == 'b') {
                // 先考虑 ab 情况下相当于右括号
                if (stack.isEmpty() || stack.peek() != 'a') {
                    return false;
                } else {
                    stack.pop();
                    // 后续还需要考虑 bc 的情况, 因此相当于左括号
                    stack.push('b');
                }
            } else {
                if (stack.isEmpty() || stack.peek() != 'b') {
                    return false;
                } else {
                    stack.pop();
                }
            }
        }
        return stack.isEmpty();
    }
}
```

### [155.最小栈](https://leetcode.cn/problems/min-stack/)
题解：

- 题意分析：设计一个能在常数时间内检索到最小元素的栈。
- 本题设计的关键难点在于：**当栈的元素 pop 出去后，最小值是可能会改变的，因此之前 push 进来的元素和最小值需要某种关系，同时这个关系还需要保存。**
- 下面是两种思路：
   - 双辅助栈：对应关系是通过另一个最小栈保存，对应关系是当前 push 的值和当前情况下的最小值。
   - 单辅助栈：对应关系是通过差值的方式保存，差值配合着当前情况的最小值可以还原实际栈中的值。
- 方案一：双辅助栈
   - 使用一个辅助栈来专门保存栈内最小值
```java
class MinStack {
    private Deque<Integer> stack;
    private Deque<Integer> minStack;

    public MinStack() {
        stack = new ArrayDeque<>();
        minStack = new ArrayDeque<>();
        minStack.push(Integer.MAX_VALUE);
    }

    public void push(int val) {
        stack.push(val);
        minStack.push(Math.min(minStack.peek(), val));
    }

    public void pop() {
        stack.pop();
        minStack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}
```

- 方案二：单辅助栈
   - 一个栈，**栈中每个元素代表的是要压入元素与当前栈中最小值的差值；**
   - 一个保存最小元素的值 min。
> 有个很重要问题：在弹出时如何维护min？
> - 因为每次压入新的元素时，压入的都是与当前栈中最小值的差值（还未压入当前元素），故在弹出元素时，若弹出了当前最小值，因为栈中记录了当前元素与【之前】最小值的差值，故根据这个记录可以更新弹出元素后的最小值。

   - `push` 方法：不光要加入栈，还需要去更新最小值；要注意栈空的情况；
   - `pop` 方法：只有在栈顶元素（val-min）小于零才需要更新最小值。
   - `top` 方法：注意这里需要的是实际的栈顶元素，因此需要对 val-min 的值做判断。
```java
class MinStack {
    
    // 栈保存当前待压入栈中的元素和当前栈最小值之前的差值: val-min
    Deque<Long> stack;

    // min 保存当前栈的最小值
    private long min;

    public MinStack() {
        stack = new ArrayDeque<>();
    }

    public void push(int val) {
        // 栈为空时，要先初始化压入 0 元素
        if (stack.isEmpty()) {
            min = val;
            stack.push(0L);
            return;
        }
        // 先压栈后更新，顺序不能颠倒，因为栈内的差值需要用后面 pop 时维护 min 使用
        stack.push((long) val - min);
        min = Math.min(val, min);

    }

    public void pop() {
        long pop = stack.pop();
        // val - min < 0: 需要更新最小值
        if (pop < 0) {
            long lastMin = min;
            min = lastMin - pop;
        }
        // val - min > 0: 不需要更新最小值
    }

    public int top() {
        // top = val - min -> val = top + min？不一定
        // top < 0: 说明最小值更新了，刚加入的元素就是最小值;
        // top = 0: 要么刚加入一个元素，要么 val = min;
        // top > 0: 说明最小值没变，那么就符合 val = top + min。
        long top = stack.peek();
        if (top <= 0) {
            return (int) min;
        } else {
            return (int) (top + min);
        }
    }

    public int getMin() {
        return (int) min;
    }
}
```

### [394.字符串解码](https://leetcode.cn/problems/decode-string/)
题解：

- 题意分析：将混合着数字括号和字母的字符串进行解码，给定一个经过编码的字符串，返回它解码后的字符串。
   - 数字表示字符串的出现次数。
   - 左右方括号用于确定字符串。
   - 字母只包含小写字母
- 基本流程：
   - 使用两个辅助栈来保存受后续遍历影响的部分：数字和字母
      - 这里用栈而不是简单的列表是因为可能出现嵌套结构，例如这种情况：`3[a2[c]]`。
   - 而左括号来控制入栈操作，右括号来控制构造字符串操作；、
   - 根据不同的字符情况来处理：
1. 是数字，则需要统计下倍数。
2. 是左括号，则需要做两件事：将之前统计过的字符串就需要停止更新同时保存到栈里面；然后重新开始新一轮的统计。
3. 是右括号，则需要开始构造当前部分组成的字符串并且拼接到结果上。构造需要出现次数（从数字栈里面得到）和单元字符串（从字符串栈中得到）。
4. 是字母，则需要添加到中间变量字符串上。
- 实现细节：
   - 这里倍数 k 是可能大于 10 的，也就是说计算 k 时需要综合处理多位数字的情况
   - 每次处理左括号前面内容后需要将重置一次 temp 变量，因为后面可能存在字符串嵌套
- NOTE：这里的 sb 保存的就是最终结果，只是遇到左括号需要暂时截断，遇到右括号后重新统计并合并。
```java
class Solution {
    public String decodeString(String s) {
        // 结果集
        StringBuilder sb = new StringBuilder();
        // 数字栈
        Deque<Integer> numStack = new ArrayDeque<>();
        // 单元字符串栈
        Deque<StringBuilder> strStack = new ArrayDeque<>();
        // 倍数
        int count = 0;
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                // 如果是数字得到倍数，需要综合处理包含多位数字的情况
                count = count * 10 + c - '0';
            } else if (c == '[') {
                // 如果是左括号，则需要保存到之前遍历得到的倍数和单元字符串
                strStack.push(sb);
                numStack.push(count);
                // 为了后续可能存在的嵌套，更新 sb 和 count 值
                sb = new StringBuilder();
                count = 0;
            } else if (Character.isLetter(c)) {
                // 包含两种情况，一种是单个字符，第二种是添加到单元字符串中
                sb.append(c);
            } else {
                // 遇到右括号则需要根据倍数构造字符串
                StringBuilder temp = strStack.pop();
                int multi = numStack.pop();
                temp.append(String.valueOf(sb).repeat(Math.max(0, multi)));
                sb = temp;
            }
        }
        return sb.toString();
    }
}
```

### [739.每日温度](https://leetcode.cn/problems/daily-temperatures/)
题解：

- 题意分析：给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。
- 单调栈模板题。
- 为什么要用单调栈：题目中的关键词解析--**下一个更大的数**
- 栈保存什么：温度下标；
- 栈内元素的单调性：自低向上递增，也就是栈顶最大；
   - 要维持栈的单调性，就要使用 while 循环去 pop 元素。
- 什么时候更新结果：每次遍历更新结果一次。
```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] ans = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = n - 1; i >= 0; i--) {
            int t = temperatures[i];
            while (!stack.isEmpty() && t >= temperatures[stack.peek()]) {
                stack.pop();
            }
            if (!stack.isEmpty()) {
                // ans 中保存下一个更高温度出现在几天后
                ans[i] = stack.peek() - i;
            }
            stack.push(i);
        }
        return ans;
    }
}
```

### [84.柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)
题解：

- 题意分析：给定一个高度数组，将其数组元素作为 y 轴，坐标为 x 轴，画出柱状图，求图中存在的最大矩阵面积。
- ![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1721800543110-6a2d80d1-f410-479e-8768-552817b2a046.png#averageHue=%23ded9d9&clientId=u2a4fdb92-9148-4&from=paste&height=256&id=ub185642f&originHeight=423&originWidth=884&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=47163&status=done&style=none&taskId=u376dc2e7-8be9-49fe-b89b-4aa12b518a2&title=&width=535.7575447916237)
- ![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1721800414634-a38c23c0-6462-4652-a230-7d1df82ce2b9.png#averageHue=%233b3b3b&clientId=u2a4fdb92-9148-4&from=paste&height=487&id=u440df559&originHeight=803&originWidth=985&originalType=binary&ratio=1.6500000953674316&rotation=0&showTitle=false&size=238656&status=done&style=none&taskId=udf9b8439-f074-40ad-b18a-ff334c522d2&title=&width=596.9696624657797)
- 如何快速地计算$left$和$right$？这可以用单调栈求出。
- **本质上思想是前缀和思想，而前缀的计算是通过单调栈实现的。**
```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        // left 表示当前位置 i 左侧小于 height 的最近下标
        int[] left = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            int x = heights[i];
            while (!stack.isEmpty() && x <= heights[stack.peek()]) {
                stack.pop();
            }
            left[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }
        // right 表示当前位置 i 右侧小于 height 的最近下标
        int[] right = new int[n];
        stack.clear();
        for (int i = n - 1; i >= 0; i--) {
            int x = heights[i];
            while (!stack.isEmpty() && x <= heights[stack.peek()]) {
                stack.pop();
            }
            right[i] = stack.isEmpty() ? n: stack.peek();
            stack.push(i);
        }
        // 最终的结果: (right - left - 1) * height
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int temp = heights[i] * (right[i] - left[i] - 1);
            ans = Math.max(ans, temp);
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [85.最大矩形](https://leetcode.cn/problems/maximal-rectangle/)  
   - [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)
   85.最大矩形
- 题意分析：要求求包含 1 的最大矩形面积
- 关键思路：思考这里矩形和非矩形的差别：在矩形中行列元素的 1 都是连续的，那么可以提前计算出列元素连续的情况，然后再去根据行元素的连续情况统计矩形面积
- 实现思路：两步：
   - 1. 根据前缀和思想，计算每个列上连续 1 的个数
   - 2. 然后后续问题就变成了 84.求柱状图的最大矩形
```java
class Solution {
    public int maximalRectangle(char[][] mat) {
        int n = mat.length, m = mat[0].length, ans = 0;
        int[][] sum = new int[n + 10][m + 10];
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                sum[i][j] = mat[i - 1][j - 1] == '0' ? 0 : sum[i - 1][j] + 1;
            }
        }
        int[] l = new int[m + 10], r = new int[m + 10];
        for (int i = 1; i <= n; i++) {
            int[] cur = sum[i];
            Arrays.fill(l, 0); 
            Arrays.fill(r, m + 1);
            Deque<Integer> d = new ArrayDeque<>();
            for (int j = 1; j <= m; j++) {
                while (!d.isEmpty() && cur[d.peekLast()] > cur[j]) r[d.pollLast()] = j;
                d.addLast(j);
            }
            d.clear();
            for (int j = m; j >= 1; j--) {
                while (!d.isEmpty() && cur[d.peekLast()] > cur[j]) l[d.pollLast()] = j;
                d.addLast(j);
            }
            for (int j = 1; j <= m; j++) ans = Math.max(ans, cur[j] * (r[j] - l[j] - 1));
        }
        return ans;
    }
}
```

42. 接雨水题解：

- 之前使用过双指针解法，这里再利用单调栈解决下问题。
- 单调栈，双指针法其实竖着看，算每个凹槽的面积，而单调栈的做法相当于「横着」计算面积，填坑
- 算法思想：找上一个更大元素，在找的过程中填坑。
   - 栈：满足从栈底到栈顶的下标对应的数组$height$中的元素递减。
```java
class Solution {
    public int trap(int[] height) {
        int n = height.length;
        int ans = 0;
        // 保存下标而不是保存值, height 数组中元素值可能重复
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && height[i] >= height[stack.peek()]) {
                int temp = height[stack.pop()];
                if (stack.isEmpty()) {
                    break;
                }
                int left = stack.peek();
                int h = Math.min(height[left], height[i]) - temp;
                ans += h * (i - left - 1);
            }
            stack.push(i);
        }
        return ans;
    }
}
```

# 队列
### [933.最近的请求次数](https://leetcode.cn/problems/number-of-recent-calls/)
题解：

- 题意分析：要求实现一个可以返回在 `[t-3000, t]` 内发生的请求数。
- 思路：直接使用队列即可。
```java
class RecentCounter {
    Queue<Integer> queue;

    public RecentCounter() {
        queue = new ArrayDeque<>();
    }
    
    public int ping(int t) {
        queue.offer(t);
        while (queue.peek() < t - 3000) {
            queue.poll();
        }
        return queue.size();
    }
}
```

### [239.滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)
题解：

- 题意分析：题目要求窗口在每次滑动的过程中的最大值，返回的是最大值数组
- 尝试：用变量保存初始窗口的最大值，滑动过程中不断判断然后更新结果——思路错误，要求每次得到的最大值都是当前窗口内的值得到。
- 明确目标：获取每次滑动后新数组中的元素最大值
   - 暴力方式下时间复杂度$O(k)$
   - 如果想降低至线性复杂度，使用单调队列或栈即可——使用单调队列，因为在滑动窗口过程中左边的元素是需要移出队列的。
- 算法思想：使用一个**单调队列**来维护当前窗口的最大值，
   - **入队：保证队列单调性，队头到队尾单调递减，之后每次去结果就从队头获得即可；**
   - **出队：维护不断移动的窗口中元素情况，如果刚移除的元素值等于队头元素值则队头元素需要移除；**
   - **收集结果：当指针等于 k-1 时开始记录，直接保存对头元素**
```java
// 保存元素下标
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] ans = new int[n - k + 1];
        Deque<Integer> q = new ArrayDeque<>(); 
        for (int i = 0; i < n; i++) {
            // 1. 入
            while (!q.isEmpty() && nums[q.getLast()] <= nums[i]) {
                q.removeLast(); // 维护 q 的单调性
            }
            q.addLast(i); // 入队
            // 2. 出
            if (i - q.getFirst() >= k) { // 队首已经离开窗口了
                q.removeFirst();
            }
            // 3. 记录答案
            if (i >= k - 1) {
                // 由于队首到队尾单调递减，所以窗口最大值就是队首
                ans[i - k + 1] = nums[q.getFirst()];
            }
        }
        return ans;
    }
}
// 保存值
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] ans = new int[n - k + 1];
        // 保存元素值
        Deque<Integer> queue = new ArrayDeque<>();
        for (int i = 0, index = 1 - k; i < n; i++, index++) {
            // 如果刚出队的元素是最大值，则需要移除队头元素
            if (index > 0 && queue.getFirst() == nums[index - 1]) {
                queue.removeFirst();
            }
            // 维持队列单调性,使用 while 循环判断
            while (!queue.isEmpty() && queue.getLast() < nums[i]) {
                queue.removeLast();
            }
            queue.addLast(nums[i]);
            // index 控制结果数组索引
            if (index >= 0) {
                ans[index] = queue.getFirst();
            }
        }
        return ans;
    }
}
```

# 二叉树 BFS
### [919.完全二叉树插入器](https://leetcode.cn/problems/complete-binary-tree-inserter/)
题解：

- 题意分析：三个操作要求：
   - 初始化：将已存在的二叉树给保存起来
   - 插入：插入完全二叉树中，同时还需要返回其父节点
   - 返回节点：返回树的根节点
- 思路：根据上述要求，只需要在保存时记录下初始根节点的位置，同时插入时基于层序遍历即可。
   - 这里使用的是 List 来模拟队列
```java
class CBTInserter {
    // 使用 List 模拟队列
    List<TreeNode> list = new ArrayList<>();
    int index = 0;
    
    public CBTInserter(TreeNode root) {
        list.add(root);
        int cur = 0;
        while (cur < list.size()) {
            TreeNode node = list.get(cur);
            if (node.left != null) {
                list.add(node.left);
            }
            if (node.right != null) {
                list.add(node.right);
            }
            cur++;
        }
    }
    
    public int insert(int val) {
        TreeNode node = new TreeNode(val);
        // 找到第一个左/右孩子不存在的节点做父节点
        while (list.get(index).left != null && list.get(index).right != null) {
            index++;
        }
        TreeNode parent = list.get(index);
        // 优先插入左孩子
        if (parent.left == null) {
            parent.left = node;
        } else if (parent.right == null) {
            parent.right = node;    
        }
        list.add(node);
        return parent.val;
    }
    
    public TreeNode get_root() {
        return list.get(0);
    }
}
```

### [515.在每个树行中找最大值](https://leetcode.cn/problems/find-largest-value-in-each-tree-row/)
题解：

- 题意分析：给定一棵二叉树的根节点 root ，找出该二叉树中每一层的最大值
- 思路：层序遍历
```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        if (root == null) {
            return ans;
        }
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            int max = Integer.MIN_VALUE;
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                max = Math.max(max, node.val);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            ans.add(max);
        }
        return ans;
    }
}
```

### [513.找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/)
题解：

- 题意分析：给定一个二叉树的 根节点 root，找出该二叉树的 最底层 最左边 节点的值。
- 思路：层序遍历， 最底层 最左边 节点就是层序遍历最后一次的起始节点
   - 注意这里简化了判断：**先入右节点，再入左节点**，这样就不需要通过层数来进行额外判断了，层序遍历的最后一个结点就是结果
```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        TreeNode node = root;
        Queue<TreeNode> queue = new ArrayDeque<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            node = queue.poll();
            if (node.right != null) {
                queue.add(node.right);
            }
            if (node.left != null) {
                queue.add(node.left);
            }
        }
        return node.val;
    }
}
```

# 网格图 BFS


# 堆
### [215.数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)
题解：

- 题意分析：给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。
- TopK 问题
- **思路一：使用大顶堆**
   - **将元素存放在一个大顶堆中，然后不断弹出堆顶元素直至第 k 个元素位置**
- 也可以使用小顶堆：第 k 个最大的元素，就是第 n-k 个最小元素。
```java
class Solution {
    // 大顶堆
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> heap = new PriorityQueue<>((o1, o2) -> (o2 - o1));
        for (int num : nums) {
            heap.offer(num);
        }
        for (int i = 0; i < k - 1; i++) {
            heap.poll();
        }
        return heap.peek();
    } 
}
```

- 时间复杂度不符合要求：$O(nlogn)$
- 怎么改进：虽然要求是返回排序后的第 k 大元素，但实际上只要这个第 k 大元素在正确的位置上时就满足了要求——快速排序思想
- **思路二：类快排思想：**
   - 利用快速排序中划分函数（左边的元素小于枢纽元素，右边的元素都大于枢纽元素），**每次划分能够确定一个元素在数组中的最终位置，再根据需要下标来选择是否进行下一步排序。**
   - 利用快速排序中划分枢纽的思想，每次划分后确定第 k 大的元素会在哪边（第 k 大的元素排序后会在$length-k$的位置），然后进行递归处理即可。
   - 算法流程：通过二分查找来检索下标是否满足要求，通过划分函数求下标
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int left = 0;
        int right = nums.length - 1;

        // 将第 k 大的元素的索引转化为下标
        int targetIndex = nums.length - k;
        // 判断元素位置
        while (left <= right) {
            int pivotIndex = partition(nums, left, right);
            if (pivotIndex == targetIndex) {
                return nums[pivotIndex];
            } else if (pivotIndex < targetIndex) {
                left = pivotIndex + 1;
            } else {
                right = pivotIndex - 1;
            }
        }
        // 处理异常情况
        return -1; 
    }
    // 划分函数
    private int partition(int[] nums, int left, int right) {
        // 快排优化：随机选择一个枢轴元素
        int randomIndex = new Random().nextInt(right - left + 1) + left;
        swap(nums, randomIndex, right);

        int pivot = nums[right];
        int pivotIndex = left;
        for (int i = left; i < right; i++) {
            if (nums[i] <= pivot) {
                swap(nums, i, pivotIndex);
                pivotIndex++;
            }
        }
        swap(nums, pivotIndex, right);
        return pivotIndex;
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

- 如果去进一步优化的话思路为：
   - 1. 直接在划分函数中应用递归，需要排除相同值；
   - 2. 使用三路快排

### [347.前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)
题解：

- 题意分析：记录一个数组中出现频率前 k 个元素，保存在数组中。也就是说这里的数字顺序是根据出现频率判断的。同样是 topK 问题，同样存在两种解法
- 思路：使用大顶堆 
   - 题目要求按频率从大到小排序，因此需要大顶堆；
   - 题目要求最后返回的是前 k 高的元素值构成的数组 m，那么堆要根据频率排序，也要保存相应的数值。
   - 入堆的元素是一个二元组$(nums[i],count)$
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 记录频率的 map
        Map<Integer, Integer> count = new HashMap<>();
        for (int x : nums) {
            count.put(x, count.getOrDefault(x, 0) + 1);
        }
        // 根据频率排序的大顶堆, o1[0]: 元素值; o1[1]: 出现频率
        PriorityQueue<int[]> heap = new PriorityQueue<>((o1, o2) -> (o2[1] - o1[1]));
        // 需要 key 和 value 时遍历 map 用 entrySet
        for (Map.Entry<Integer, Integer> map : count.entrySet()) {
            heap.offer(new int[]{map.getKey(), map.getValue()});
        }
        int[] ans = new int[k];
        for (int i = 0; i < k; i++) {
            ans[i] = heap.poll()[0];
        }
        return ans;
    }
}
```

- 时间复杂度: $O(nlog(k))$；空间复杂度：$O(n)$

### [373.查找和最小的 K 对数字](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)
题解：

- 题意分析：从两个非递减数组中分别选择一个数构成一个二元组，要求返回最小的 k 个数对
- 目标是根据两个元素之和从小到大排列，返回前 k 对数组元素的值
- 思路：**使用小顶堆，将三元组**$(nums1[i] + nums2[j], i, j)$**入堆，这样可以根据 **$x[0]$**来进行排序，根据**$x[1],x[2]$**来构造结果。**
```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        int n = nums1.length, m = nums2.length;
        var ans = new ArrayList<List<Integer>>(k); // 预分配空间
        var pq = new PriorityQueue<int[]>(n * m, (a, b) -> a[0] - b[0]);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                pq.offer(new int[]{nums1[i] + nums2[j], i, j});
            }
        }
        for (int index = 0; index < k; index++) {
            int[] temp = pq.poll();
            int i = temp[1], j = temp[2];
            ans.add(List.of(nums1[i], nums2[j]));
        }
        return ans;
    }
}
```

- 优化：**上述思路保存很多无意义的数值，我们只关心前 k 组元素对而且没有用到数组初始已排序的性质**，可以明确的是从$(0,0)$开始然后两边增大不断比较$(i+1,j)$和$(i,j+1)$
   - 我们可以先保存一半$(i,0)$，然后在遍历的过程中边更新结果边添加另一半边。因为数组初始已排序。
   - 默认$(0,0)$是最小
```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        int n = nums1.length, m = nums2.length;
        List<List<Integer>> ans = new ArrayList<>(k);
        PriorityQueue<int[]> heap = new PriorityQueue<>((o1, o2) -> (o1[0] - o2[0]));
        for (int i = 0; i < Math.min(n, k); i++) {
            heap.offer(new int[]{nums1[i] + nums2[0], i, 0});
        }
        while (!heap.isEmpty() && ans.size() < k) {
            int[] temp = heap.poll();
            int i = temp[1], j = temp[2];
            ans.add(List.of(nums1[i], nums2[j]));
            if (j + 1 < m) {
                heap.add(new int[]{nums1[i] + nums2[j + 1], i, j + 1});
            }
        }
        return ans;
    }
}
```

### [703.数据流中的第 K 大元素](https://leetcode.cn/problems/kth-largest-element-in-a-stream/)
题解：

- 题意分析：要求设计一个数据结构满足能快速找到当前数据流第 k 大的元素。
- 思路：TopK 问题，这里使用一个小顶堆来保存数据
- 可以使用一个大小为 k 的优先队列来存储前 k 大的元素，其中优先队列的队头为队列中最小的元素，也就是第 k 大的元素。
- 在单次插入的操作中，我们首先将元素 val 加入到优先队列中。如果此时优先队列的大小大于 k，我们需要将优先队列的队头元素弹出，以保证优先队列的大小为 k
```java
class KthLargest {
    private PriorityQueue<Integer> heap;
    private int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        heap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        heap.offer(val);
        if (heap.size() > k) {
            heap.poll();
        }
        return heap.peek();
    }
}
```

### [295.数据流的中位数](https://leetcode.cn/problems/find-median-from-data-stream/)
题解：

- 题意分析：求有序列表中的中位数，求中位数只关心中间的一个位置或两个位置
- 使用两个堆保存：一个堆保存左半部分——大顶堆，一个堆保存右半部分——小顶堆
- 区分大小的奇偶的情况：
   - 规定小顶堆保存$[left,mid]$的元素，大顶堆保存 $(mid, right]$的元素。
   - 如果小顶堆大小大于大顶堆说明总大小为奇数情况，返回小顶堆堆顶；
   - 反之则返回两堆顶平均值。
- 当我们尝试添加一个数num到数据结构中，我们需要分情况讨论(核心要求是两个堆的大小不能失衡$min.size()+1==max.size()$)：
1. $num≤max⁡[queMin]$
- 此时 num小于等于中位数，我们需要将该数添加到 queMin中。新的中位数将小于等于原来的中位数，因此我们可能需要将 queMin中最大的数移动到 queMax中。
2. $num>max⁡[queMin]$
- 此时num大于中位数，我们需要将该数添加到queMax中。新的中位数将大于等于原来的中位数，因此我们可能需要将 queMax中最小的数移动到queMin中。
- 特别地，当累计添加的数的数量为0时，我们将num添加到 queMin中。
```java
class MedianFinder {
    PriorityQueue<Integer> queMin;
    PriorityQueue<Integer> queMax;

    public MedianFinder() {
        // 大顶堆
        queMin = new PriorityQueue<>((a, b) -> (b - a));
        // 小顶堆
        queMax= new PriorityQueue<>((a, b) -> (a - b));
    }

    public void addNum(int num) {
        if (queMin.isEmpty() || num <= queMin.peek()) {
            queMin.offer(num);
            if (queMax.size() + 1 < queMin.size()) {
                queMax.offer(queMin.poll());
            }
        } else {
            queMax.offer(num);
            if (queMax.size() > queMin.size()) {
                queMin.offer(queMax.poll());
            }
        }
    }

    public double findMedian() {
        if (queMin.size() > queMax.size()) {
            return queMin.peek();
        }
        return (queMin.peek() + queMax.peek()) / 2.0;
    }
}
```

- 同类型的题目：
   - [155.最小栈](https://leetcode.cn/problems/min-stack/)
   - [1670.设计前中后队列](https://leetcode.cn/problems/design-front-middle-back-queue/)
   1670.设计前中后队列题解：

```java
class FrontMiddleBackQueue {
    private final Deque<Integer> left = new ArrayDeque<>();
    private final Deque<Integer> right = new ArrayDeque<>();

    // 调整长度，保证 0 <= right.size() - left.size() <= 1
    // 从而保证可以在正中间插入删除元素
    private void balance() {
        if (left.size() > right.size()) {
            right.addFirst(left.pollLast());
        } else if (right.size() > left.size() + 1) {
            left.addLast(right.pollFirst());
        }
    }

    public void pushFront(int val) {
        left.addFirst(val);
        balance();
    }

    public void pushMiddle(int val) {
        if (left.size() < right.size()) {
            left.addLast(val);
        } else {
            right.addFirst(val);
        }
    }

    public void pushBack(int val) {
        right.addLast(val);
        balance();
    }

    public int popFront() {
        if (right.isEmpty()) { // 整个队列为空
            return -1;
        }
        int val = left.isEmpty() ? right.pollFirst() : left.pollFirst();
        balance();
        return val;
    }

    public int popMiddle() {
        if (right.isEmpty()) { // 整个队列为空
            return -1;
        }
        if (left.size() == right.size()) {
            return left.pollLast();
        }
        return right.pollFirst();
    }

    public int popBack() {
        if (right.isEmpty()) { // 整个队列为空
            return -1;
        }
        int val = right.pollLast();
        balance();
        return val;
    }
}
```

# 贪心
### [121.买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)
题解：

- 题意分析：要求一次买入卖出后得到的收益最大
- 贪心思想：如果买入时候价格最低，后续卖出得到的收益期望才能最大，那么每次遍历都做两件事：计算可能的最大收益；更新可能的最低买入价格。
   - 局部最优：买入价格最低
   - 全局最优：卖出后收益最大
```java
class Solution {
    public int maxProfit(int[] prices) {
        int ans = 0;
        int minPrice = prices[0];
        for (int p : prices) {
            ans = Math.max(ans, p - minPrice);
            minPrice = Math.min(minPrice, p);
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [122.买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)
   - [123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)
   - [188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/) 
   - [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) 
   - [714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
   122.买卖股票的最佳时机 II题解：

- 题意分析：与上题的差别在于本题可以重复交易，也就是通过多次买卖来赚取收益，需要注意的是一次只能持有一个股票
- 贪心思想：
   - 局部最优：每次交易都是赚钱的，
   - 全局最优：将全部赚钱的交易累计
- 算法流程：不断遍历，两两累计收益即可。
```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        int n = prices.length;
        for (int i = 1; i < n; i++) {
            int profit = prices[i] - prices[i - 1];
            if (profit > 0) {
                res += profit;
            }
        }
        return res;
    }
}
```
123. 买卖股票的最佳时机 III题解：

- 题意分析：本题相比上题又增加了一个限制条件：交易次数不能超过两次。
- 首先明确一点：两次收益为正的交易一定好于一次交易，那么这个问题就变成了两次 121 题，但又存在着一个限制：不能持有多只股票。
- 动态规划：基于状态 dp 思考，将购买股票分为四种状态：第一次买和卖，第二次买和卖。
   - dp 数组含义以及收集结果的位置：dp[i][j] 表示到第 i 天做 j 类操作的收益，收集结果的位置为最后一天操作的最大值$Math.max(dp[n-1][1], dp[n-1]][3])$
   - 状态转移方程：基于选或不选去思考：选就是对应当前的买入/卖出，不选就是对应前一个状态。
      - `dp[i][0] = Math.max(dp[i - 1][0], -prices[i]);`
      - `dp[i][1] = Math.max(dp[i - 1][1], dp[i][0] + prices[i]);`
      - `dp[i][2] = Math.max(dp[i - 1][2], dp[i][1] - prices[i]);`
      - `dp[i][3] = Math.max(dp[i - 1][3], dp[i][2] + prices[i]);`
   - dp 数组初始化：初始化开始的两个买入状态即可，其他默认初始化。
   - 遍历顺序：从左向右遍历，dp 数组计算从 0 到 3。
```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n == 0) {
            return 0;
        }
        int[][] dp = new int[n][4];
        dp[0][0] = -prices[0];
        dp[0][2] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], -prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i][0] + prices[i]);
            dp[i][2] = Math.max(dp[i - 1][2], dp[i][1] - prices[i]);
            dp[i][3] = Math.max(dp[i - 1][3], dp[i][2] + prices[i]);
        }
        return Math.max(dp[n - 1][1], dp[n - 1][3]);
    }
}
```

- 可以优化的方向：空间压缩，直接使用一维滚动数组即可。
```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n == 0) {
            return 0;
        }
        int[] dp = new int[4];
        dp[0] = -prices[0];
        dp[2] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[0] = Math.max(dp[0], -prices[i]);
            dp[1] = Math.max(dp[1], dp[0] + prices[i]);
            dp[2] = Math.max(dp[2], dp[1] - prices[i]);
            dp[3] = Math.max(dp[3], dp[2] + prices[i]);
        }
        return Math.max(dp[1], dp[3]);
    }
}
```
188. 买卖股票的最佳时机 IV题解：

- 题意分析：上题的延伸版：可交易次数从 2 次提高到 k 次
- 思路完全一样：状态 dp
```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int n = prices.length;
        if (n <= 1) {
            return 0;
        }
        int[] dp = new int[2 * k];
        for (int i = 0; i < 2 * k; i += 2) {
            dp[i] = -prices[0];
        }
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < 2 * k; j++) {
                if (j == 0) {
                    dp[j] = Math.max(dp[j], -prices[i]);
                } else {
                    if (j % 2 == 0) {
                        dp[j] = Math.max(dp[j], dp[j - 1] - prices[i]);
                    } else {
                        dp[j] = Math.max(dp[j], dp[j - 1] + prices[i]);
                    }
                }
            }
        }
        int ans = 0;
        for (int i = 1; i < 2 * k; i += 2) {
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }
}
```

- 简化状态定义：dp[i][0] 为第 i 次买，dp[i][1] 为第 i 次卖：
```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int[][] f = new int[k + 2][2];
        for (int j = 1; j <= k + 1; j++) {
            f[j][1] = Integer.MIN_VALUE / 2; // 防止溢出
        }
        f[0][0] = Integer.MIN_VALUE / 2;
        for (int p : prices) {
            for (int j = k + 1; j > 0; j--) {
                f[j][0] = Math.max(f[j][0], f[j][1] + p);
                f[j][1] = Math.max(f[j][1], f[j - 1][0] - p);
            }
        }
        return f[k + 1][0];
    }
}
```
309. 最佳买卖股票时机含冷冻期
714. 买卖股票的最佳时机含手续费

### [55.跳跃游戏](https://leetcode.cn/problems/jump-game/)
题解：

- 题意分析：数组元素指在当前位置能跳跃的距离，判断是否能从下标 0 的位置跳跃到最后一个下标。需要注意的是每次的跳跃的距离可以在$[1,nums[i]]$之间
- 贪心思想：每次都尽力去跳最大位置，如果能跳到最后一个下标说明满足条件。那么每次遍历都做两件事：更新能跳到的最大距离；判断是否能继续往后跳。
```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int max = 0;
        for (int i = 0; i < n - 1; i++) {
            // 判断是否能继续往后跳
            if (max < i) {
                break;
            }
            max = Math.max(max, nums[i] + i);
        }
        return max >= n - 1;
    }
}
```

- 同类型的题目：
   - [45.跳跃游戏 II](https://leetcode.cn/problems/jump-game-ii/)
   45.跳跃游戏 II题解：

- 题意分析：和上题的区别在于结果要求返回最小的跳跃步数
- 贪心思想：基本思路与上题相似，关键是如果要最小的跳跃步数，那么每一步都尽可能的大，也就是说每次都去跳可能的最大位置。
- 做一个预判：跳到的位置后能覆盖的范围最大，因此需要记录两个值：当前位置起跳能覆盖的范围和下一步起跳后能覆盖的范围。
```java
class Solution {
    public int jump(int[] nums) {
        if (nums.length == 1) {
            return 0;
        }
        // 当前覆盖的最远距离下标
        int cur = 0;
        // 下一步能覆盖的最远距离下标
        int next = 0;
        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            next = Math.max(next, nums[i] + i);
            // 如果当前能够覆盖整个数组直接退出
            if (cur >= nums.length - 1) {
                break;
            }
            // 遇到当前覆盖的最远距离下标，则直接更新，累加步数
            if (i == cur) {
                cur = next;
                res++;
            }
        }
        return res;
    }
}
```

### [763.划分字母区间](https://leetcode.cn/problems/partition-labels/)
题解：

- 题意分析：要求将字符串尽可能多的划分成片段
- 要求将同一个字母都划分在一个片段内（也就是说这个片段内所有字母的最后一次出现位置都在区间内），本质上就是要求找每个字母的边界（使用哈希表记录每个字母最后一次出现的位置），遍历到当前片段的最远边界就说明左边已经包含了全部的字母了。
- 实现步骤：
   - 统计每一个字符最后出现的位置
   - 从头遍历字符，并更新字符的最远出现下标，**如果找到字符最远出现位置下标和当前下标相等了**，则找到了分割点
```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        // 存储每个字母最后出现的索引
        int[] lastIndices = new int[26]; 
        for (int i = 0; i < s.length(); i++) {
            lastIndices[s.charAt(i) - 'a'] = i;
        }

        List<Integer> partitions = new ArrayList<>();
        // 当前片段的起始位置
        int start = 0; 
        // 当前片段的结束位置
        int end = 0; 
        for (int i = 0; i < s.length(); i++) {
            end = Math.max(end, lastIndices[s.charAt(i) - 'a']);
            // 当前位置是当前片段的结束位置
            if (i == end) { 
                partitions.add(end - start + 1);
                // 下一个片段的起始位置
                start = end + 1; 
            }
        }

        return partitions;
    }
}
```

# 动态规划
### [70.爬楼梯](https://leetcode.cn/problems/climbing-stairs/)
题解：

- 选或不选思考递推公式
- 动归五部曲
```java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        if (n <= 1) {
            return 1;
        }
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}

// 原地操作版
class Solution {
    public int climbStairs(int n) {
        int dp0 = 1, dp1 = 2;
        if (n <= 1) {
            return 1;
        }
        for (int i = 2; i < n; i++) {
            int temp = dp0 + dp1;
            dp0 = dp1;
            dp1 = temp;
        }
        return dp1;
    }
}
```

- 同类型的题目：
   - [746.使用最小花费爬楼梯](https://leetcode.cn/problems/min-cost-climbing-stairs/)
   746.使用最小花费爬楼梯题解：

- 题意分析：根据给定的花费选择一条花费最小的路径到达数组尾部。
- 算法思想：动态规划
   - 基于选或不选思考。
   - 动归五部曲。
```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 0;
        for (int i = 2; i <= n; i++) {
            dp[i] = Math.min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]);
        }
        return dp[n];
    }

    // 空间复杂度: O(1)
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        int dp0, dp1;
        dp0 = 0;
        dp1 = 0;
        for (int i = 2; i <= n; i++) {
            int dpi = Math.min(dp0 + cost[i - 2], dp1 + cost[i - 1]);
            dp0 = dp1;
            dp1 = dpi;
        }
        return dp1;
    }
}
```

### [118.杨辉三角](https://leetcode.cn/problems/pascals-triangle/)
题解：

- 递归公式$dp[i][j]=dp[i-1][j-1]+dp[i-1][j]$
- 动归五部曲
```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();
        int[][] dp = new int[numRows + 1][numRows + 1];
        dp[0][0] = 1;
        for (int i = 1; i <= numRows; i++) {
            List<Integer> arr = new ArrayList<>();
            for (int j = 1; j <= i; j++) {
                dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                arr.add(dp[i][j]);
            }
            ans.add(arr);
        }
        return ans;
    }
}
```

### [198.打家劫舍](https://leetcode.cn/problems/house-robber/)
题解：

- 题意分析：给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。
   - 不触动警报装置的要求：相邻的房屋不能同时选择。
- 基于选或不选思考。
- 算法思想：动态规划
```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        // 可以省去边界判断
        int[] dp = new int[n + 2];
        for (int i = 0; i < n; i++) {
            dp[i + 2] = Math.max(dp[i + 1], dp[i] + nums[i]);
        }
        return dp[n + 1];
    }

    // 原地操作版
    public int rob(int[] nums) {
        int ans = 0;
        int dp0 = 0, dp1 = 0;
        for (int x : nums) {
            // 滚动更新, 过程中记录最大值
            ans = Math.max(dp0 + x, dp1);
            dp0 = dp1;
            dp1 = ans;
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [213.打家劫舍 II](https://leetcode.cn/problems/house-robber-ii/)
   - [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)
   - [2560. 打家劫舍 IV](https://leetcode.cn/problems/house-robber-iv/)
   213.打家劫舍 II题解：

- 题意分析：与上题的差别在于处理相邻的房屋不能选之外，首尾也不能同时选。
- 优化方式：直接对头尾进行提前判断：
   - 偷`nums[0]`：那么`nums[1]`和`nums[n-1]`都不能偷——`nums[0] + rob1[2, n - 1)`；
   - 不偷`nums[0]`：那么`nums[1]`和`nums[n-1]`都能偷——`rob1[1, n)`
   - 结果就是取两种情况的最大值。
- 计算正常部分的值就直接使用上一题的基础模型，修改为指定的区间即可。
```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        return Math.max(nums[0] + rob1(nums, 2, n - 1), rob1(nums, 1, n));
    }

    // [start, end) 以避免单元素边界情况的讨论
    private int rob1(int[] nums, int start, int end) {
        int ans = 0;
        int dp0 = 0, dp1 = 0;
        for (int i = start; i < end; i++) {
            ans = Math.max(dp0 + nums[i], dp1);
            dp0 = dp1;
            dp1 = ans;
        }
        return ans;
    }
}
```
337. 打家劫舍 III题解：

- 首先做的一个预处理就是对结点状态的分析：每个结点就两种状态：选或不选
- 树形dp：抽象为树形结构，从叶子结点向上考虑： 
   - 选：左子树不选的值+右子树不选的值+自身结点值
   - 不选：max(左选，左不选) + max(右选，右不选)
   - 最终结果：max(根选，根不选)
- 算法实现：**使用一个数组保存结点状态值，使用递归算法，不断向上返回结果。**
```java
class Solution {
    public int rob(TreeNode root) {
        int[] res = dfs(root);
        return Math.max(res[0], res[1]); // 根节点选或不选的最大值
    }

    private int[] dfs(TreeNode node) {
        if (node == null) // 递归边界
            return new int[]{0, 0}; // 没有节点，怎么选都是 0
        int[] left = dfs(node.left);  // 递归左子树
        int[] right = dfs(node.right); // 递归右子树
        // 选
        int rob = left[1] + right[1] + node.val;
        // 不选 
        int notRob = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);  
        return new int[]{rob, notRob};
    }
}
```
2560. 打家劫舍 IV题解：

- 题意分析：要求返回小偷的最小窃取能力。而窃取能力的计算是选择 k 个房屋中最大的那个值。
- 分解下题目信息：
   - 基本的选择要求：相邻的元素不能选。
   - k 表示的是一次选择可以选多少个房屋。
   - 单个窃取能力是$\max_{i=1,2,\ldots,k} nums[i]$
   - 要求返回的结果的所有可以选择的情况中最小的值。
- 二分+动态规划：
```java
class Solution {
    public int minCapability(int[] nums, int k) {
        int left = 0, right = 0;
        for (int x : nums) {
            right = Math.max(x, right);
        }
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (check(nums, k , mid)) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return right;
    }

    private boolean check(int[] nums, int k, int target) {
        int dp0 = 0, dp1 = 0;
        for (int x : nums) {
            if (x > target) {
                dp0 = dp1;
            } else {
                int temp = dp1;
                dp1 = Math.max(dp0 + 1, dp1);
                dp0 = temp;
            }
        }
        return dp1 >= k;
    }
}
```

- 二分+贪心：
```java
class Solution {
    public int minCapability(int[] nums, int k) {
        int max = nums[0];
        int min = nums[0];
        for (int num : nums) {
            max = Math.max(max, num);
            min = Math.min(min, num);
        }
        int left = min;
        int right = max;
        int mid;
        while (left < right) {
            mid = left + ((right - left) >> 1);
            // 数组中元素都小于等于 mid 且元素都不相邻的元素个数
            int count = 0;
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] <= mid) {
                    count++;
                    // 避免元素相邻
                    i++;
                }
            }
            if (count < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
}
```

### [279.完全平方数](https://leetcode.cn/problems/perfect-squares/)
题解：

- 选或不选
- 背包问题：物品就是这里的完全平方数，书包就是这里的 n（完全背包问题，这里的完全平方数可以被重复选取）
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：dp[i] 指和为 i 时存在的完全平方数的最少数量，在 dp[n] 时返回结果
   - 递归公式：选或不选：$dp[j]=Math.min(dp[j], dp[j - i*i] + 1)$
   - dp 数组初始化：后面要取 min， 初始化为最大值即可。dp[0]=0 以防止 n=1 时出现错误。
   - 遍历顺序：先遍历物品再遍历书包容量。
```java
class Solution {
    public int numSquares(int n) {
        int max = Integer.MAX_VALUE;
        int[] dp = new int[n + 1];
        Arrays.fill(dp, max);
        dp[0] = 0;
        for (int i = 1; i * i <= n; i++) {
            for (int j = i * i; j <= n; j++) {
                if (dp[j - i * i] != max) {
                    // 选或不选
                    dp[j] = Math.min(dp[j], dp[j - i * i] + 1);
                }
            }
        }
        return dp[n];
    }
}
```

### [322.零钱兑换](https://leetcode.cn/problems/coin-change/)
题解：

- 题意分析：计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。
   - 每种硬币的数量是无限的。
- 基于选或不选去思考递推公式
- **完全背包问题：硬币是物品，总金额是背包**
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：`dp[i]` 是指当总金额为 $i$ 时需要的最小硬币个数，在 $dp[amount]$ 时返回结果 。
   - 递推公式：标准的背包问题递归公式：$dp[j] = Math.min(dp[j],dp[j-coins[i]] + 1)$
   - dp 数组初始化：后面要取 min， 初始化为最大值即可。dp[0]=0 以防止 n=1 时出现错误。
   - 遍历顺序：先遍历物品再遍历背包（先遍历背包再遍历物品同样可以）
```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        int max = amount + 1;
        Arrays.fill(dp, max);
        dp[0] = 0;
        for (int coin : coins) {
            for (int j = coin; j <= amount; j++) {
                if (dp[j - coin] != max) {
                    dp[j] = Math.min(dp[j], dp[j - coin] + 1);
                }
            }
        }
        return dp[amount] == max ? -1 : dp[amount];
    }
}
```

- 同类型的题目：
   - [518.零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)
   - [377.组合总和 Ⅳ](https://leetcode.cn/problems/combination-sum-iv/)
   518.零钱兑换 II题解：

- 题意分析：与上题的差别在于本题要求计算的是能够凑成 $amount$ 的组合个数，
- 将硬币看出步数，问题就很像爬楼梯：从右往左思考，基于选或不选
- 动归五部曲：
   - dp 数组含义以及收集结果的位置：`dp[i]`表示金额为 i 时可以凑成的总金额数，最终结果为 `dp[amount]`
   - 递推公式：基于选或不选思考$dp[j] += dp[j - coins[i]]$
   - dp 数组初始化：`dp[0]` 为 1 即可，其他位置遍历过程中确定
   - 遍历顺序：先遍历物品再遍历背包
```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for (int coin : coins) {
            for (int j = coin; j <= amount; j++) {
                dp[j] += dp[j - coin];
            }
        }
        return dp[amount];
    }
}
```
377.组合总和 Ⅳ题解：

- 题意分析：给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。
- 本质上与上题没什么差别，关键在于题目中的一句话：**请注意，顺序不同的序列被视作不同的组合。**
   - **这意味着本题中先遍历背包和遍历物品的顺序是有要求的。**
   - nums 为物品，物品的先后顺序是不同的组合，因此要先遍历背包再遍历物品。
- 算法思想：动态规划
   - 基于选或不选
   - 动归五部曲
```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        // dp[i] 表示当目标值为 i 时存在的方案数
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            // 递推公式
            for (int x : nums) {
                if (i - x >= 0) {
                    dp[i] += dp[i - x];
                }
            }
        }
        return dp[target];
    }
}
```

### [494.目标和](https://leetcode.cn/problems/target-sum/)
题解：

- 题意分析：给你一个非负整数数组 `nums` 和一个整数 `target` 。向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 表达式，如果这个表达式的值是 `target`，那么则符合要求。计算所有符合要求的情况。
   - target 题目中是可能会负的。
- 将问题转换成转换多少个负数能达到 $target$，然后不同顺序的负数转换是不同的方案数。
   - 负数个数符合要求情况的数学表达：$\frac{s-|target|}2$
   - 显然如果上述表达式如果小于 0 或者说为奇数，说明一定不存在方案数。
- 背包问题：01 背包，物品不是重复选的。
- 算法思想：动态规划，基于选或不选思考。
   - dp 数组：dp[i] 表示到达当前下标值存在的表达式情况。
   - 递推公式：
   - 遍历顺序：这里做了一维是滚动数组优化，因此需要先遍历物品，再反向遍历背包。
      - 先遍历物品再遍历背包是因为是 01 背包问题，物品的个数是有限的。
      - 反向遍历背包是因为计算当前位置的值需要上一轮的前面位置数组元素的情况，而滚动数组下上一轮前面位置的计算结果会被覆盖掉，因此选择反向遍历，前面的位置还没被计算过。
      - 总结下一维滚动数组的遍历顺序就是从上到下遍历物品，从右到左遍历背包。
   - dp 数组初始化：
```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for (int x : nums) {
            sum += x;
        }
        sum -= Math.abs(target);
        if (sum < 0 || sum % 2 == 1) {
            return 0;
        }
        int negTarget = sum / 2;
        int[] dp = new int[negTarget + 1];
        dp[0] = 1;
        for (int x : nums) {
            for (int j = negTarget; j >= x; j--) {
                dp[j] += dp[j - x];
            }
        }
        return dp[negTarget];
    }
}
```

### [139.单词拆分](https://leetcode.cn/problems/word-break/)
题解：

- 题意分析：给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 `s` 则返回 `true`。
   - 不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。——完全背包。
- 分析题意可以得到，该题为背包问题：字典是物品，字符串是背包，又因字典内的单词可重复使用，为完全背包问题。
- 基于选或不选思考，动态五部曲：
   - dp 数组和下标的含义以及收集结果的位置：dp[i] 表示当字符串长度为 i 时是否能匹配成功，最终结果为 dp[n]
   - 递归公式：没有具体的递归公式，就是长度够了同时匹配成功同时前面匹配情况也是成功的才算成功
   - dp 数组初始化：默认为 false，dp[0] 位置初始化为 true
   - 遍历顺序：一定要先背包后物品，因为要求是字符串匹配，要搜索当前段所有可能的匹配情况。
```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int j = 1; j <= n; j++) {
            for (String word : wordDict) {
                int i = word.length();
                // 选:
                if (j >= i && dp[j - i] && word.equals(s.substring((j - i), j))) {
                    dp[j] = true;
                    break;
                }
                // 不选: dp 数组默认值为 false
            }
        }
        return dp[n];
    }
}
```

### [300.最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)
题解：

- 题意分析：题目要求找出其中最长严格递增子序列的长度。
   - 子序列要求：元素可以删，相对顺序需要保持。
- 这里关键是对构造子序列的要求——可以删除元素，那么也就是说最长子序列中最右边的元素是固定，那么就可以根据这个条件构造 dp 数组。
- 基于选或不选思考。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：dp[i] 表示以下标 i 为结尾的最长递增子序列长度，结果是每次遍历都去更新一下最长序列大小
   - 递推公式：标准的背包问题递归公式：$dp[j] = Math.max(dp[j],dp[j-1] + 1)$
   - dp 数组初始化：全部初始化为 1，默认最小的子序列长度为 1
   - 遍历顺序：没有要求，从左向右遍历
```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        int ans = 1;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                // 选/不选
                if (nums[j] < nums[i]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            // 要求存在的最长子序列, 不断更新结果
            ans = Math.max(ans, dp[i]);
        }
        return ans;
    }
}
```

- 时间复杂度更优的解法（$O(nlogn)$）：贪心+二分
- 

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int ans = 0;
        for (int num : nums) {
            int j = lowerBound(nums, ans, num);
            nums[j] = num;
            if (j == ans) {
                ans++;
            }
        }
        return ans;
    }

    private int lowerBound(int[] nums, int right, int target) {
        int left = -1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid;
            } else {
                right = mid;
            }
        }
        return right;
    }
}
```

### [416.分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)
题解：

- 题意分析：将数组中的元素分成两份，满足两份元素之和相等。
- 首先如果数组元素和为奇数一定不能平分，然后将问题简化为能不能找到子集和满足数组和的一半
- 背包问题：原数组元素为物品，数组和的 一半为背包，数组元素不能重复选取，因此是 01 背包问题。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：$dp[i]$ 表示当目标元素和为 i 时，数组内能构成的元素和，当构成的元素和等于 $\frac{sum}{2}$ 说明为 true，其他情况为 false。 
   - 递推公式：标准的背包问题的递归公式（基于选或不选得到）：$dp[j] = Math.max(dp[j],\ dp[j-nums[i]] + nums[i])$
   - dp 数组初始化：默认初始化，无其他要求
   - 遍历顺序：**先遍历物品，再遍历背包，同时背包要求倒序遍历，这是优化一维滚动数组后 01 背包问题基本固定的遍历方式。**
```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0;
        for (int i : nums) {
            sum += i;
        }
        if (sum % 2 != 0) {
            return false;
        }
        sum /= 2;
        int[] dp = new int[sum + 1];
        // 先遍历物品
        for (int i = 0; i < nums.length; i++) {
            // 再遍历背包, 背包要求倒序遍历
            for (int j = sum; j >= nums[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - nums[i]] + nums[i]); 
            }
            if (dp[sum] == sum) {
                return true;
            }
        }
        return dp[sum] == sum;
    }
}
```

### [32.最长有效括号](https://leetcode.cn/problems/longest-valid-parentheses/)
题解：

- 题意分析：给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。
   - 有效括号子串的要求：左右括号要匹配上同时要求连续。
- 对于括号匹配问题，可以使用栈完成。
```java
class Solution {
    public int longestValidParentheses(String s) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(-1);
        int ans = 0;
        char[] chs = s.toCharArray();
        for (int i = 0; i < chs.length; i++) {
            // 左括号入栈
            if (chs[i] == '(') {
                stack.push(i);
            } else { // 右括号匹配
                stack.pop();
                if (stack.isEmpty()) {
                    stack.push(i);
                } else {
                    ans = Math.max(ans, i - stack.peek());
                }
            }
        }
        return ans;
    }
}
```

- 可以使用动态规划方式求解，这需要去考虑对于左右括号状态的处理——选或不选
   - 注意这里要求最长的有效括号情况，这需要在遍历的过程中去更新结果。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：dp[i] 表示以 i 为结尾的最长有效括号长度，因为要求最长子串，每次遍历都去更新一次结果。
   - 递归公式：基于选或不选思考：
      - 当 s[i] 为'('时，dp[i] 等于 0；
      - 当 s[i] 为')'时，则需要去向前匹配，当 s[i-1] 为'('时，则$dp[i]=dp[i-2] + 2$；当 s[i-1] 为')'时，则需要去再向前寻找：$dp[i]=dp[i-dp[i-1]-2]+dp[i-1]+2$
   - dp 数组初始化：默认初始化为 0
   - 遍历顺序：从左向右遍历
```java
class Solution {
    public int longestValidParentheses(String s) {
        int n = s.length();
        int ans = 0;
        int[] dp = new int[n];
        for (int i = 1; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == ')') {
                if (s.charAt(i - 1) == '(') {
                    // () 匹配成功, 再继续向前判断
                    dp[i] = (i - 2 >= 0 ? dp[i - 2] : 0) + 2;
                } else {
                    // )) 情况需要向前判断
                    int j = i - dp[i - 1] - 1;  // 对应的需要判断是否为 ( 的位置
                    if (j >= 0 && s.charAt(j) == '(') {
                        dp[i] = dp[i - 1] + 2 + (j - 1 >= 0 ? dp[j - 1] : 0);
                    }
                }
            }
            ans = Math.max(dp[i], ans);
        }
        return ans;
    }
}
```

### [LCR 091.粉刷房子](https://leetcode.cn/problems/JEj789/)
题解：

- 题意分析：每次都选最小成本去粉刷，计算出粉刷完所有房子最少的花费成本，要求相邻的房子间不能粉刷成相同颜色。
- 思路：动态规划——状态机 DP
   - 本质上这是一道「状态机 DP」问题：某些状态只能由规则限定的状态所转移，通常我们可以从 $f[i][j]$ 能够更新哪些目标状态（后继状态）进行转移，也能够从 $f[i][j]$ 依赖哪些前置状态（前驱状态）来转移。
   - 一些细节：考虑到我们 $f[i][X]$ 的计算只依赖于 $f[i−1][X]$，因此我们可以使用三个变量来代替我们的动规数组。
- 对于本题来说，每次更新结果就是判断选最小的，同时要满足限制条件
```java
class Solution {
    public int minCost(int[][] costs) {
        int n = costs.length;
        int a = costs[0][0], b = costs[0][1], c = costs[0][2];
        for (int i = 1; i < n; i++) {
            int d = Math.min(b, c) + costs[i][0];
            int e = Math.min(a, c) + costs[i][1];
            int f = Math.min(a, b) + costs[i][2];
            a = d;
            b = e;
            c = f;
        }
        return Math.min(a, Math.min(b, c));
    }
}
```

# 多维动态规划
### [62.不同路径](https://leetcode.cn/problems/unique-paths/)
题解：

- 题意分析：求机器人到终点的路径数
   - 一个机器人位于一个 `m x n` 网格的左上角，坐标表示就是$(0,0)$
   - 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角。
- 基于选或不选思考，dp 数组需要保存可能存在的路径数。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：$dp[i][j]$表示到达坐标(i,j) 位置的不同路径数，$dp[m-1][n-1]$为最终结果
   - 递推公式：根据选或不选得到：$dp[i][j]=dp[i-1][j]+dp[i][j-1]$
   - dp 数组初始化：初始化上边界和左边界为 1，(0,0)位置也为 1（如果为 1x1 矩阵结果为 1）
   - 遍历顺序：从左往右，从上往下。
```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        for (int i = 1; i < m; i++) {
            dp[i][0] = 1;
        }
        for (int j = 1; j < n; j++) {
            dp[0][j] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                // 选 / 不选
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]; // 向右 / 向下
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

- 同类型的题目：
   - [63.不同路径 II](https://leetcode.cn/problems/unique-paths-ii/)
   63.不同路径 II题解：

- 与上题的区别在于出现了障碍物：障碍物带来的影响在于如果遇到了之后的路就都走不通，也就说初始化时遇到了障碍物后续的遍历都为 0，遍历时遇到了障碍物直接跳过。
- 在之前的代码上添加上关于判断障碍物的逻辑即可。
```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid[0][0] == 1) {
            return 0;
        }
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = 1;
        for (int i = 1; i < m; i++) {
            if (obstacleGrid[i][0] == 1) {
                break;
            }
            dp[i][0] = 1;
        }
        for (int j = 1; j < n; j++) {
            if (obstacleGrid[0][j] == 1) {
                break;
            }
            dp[0][j] = 1;
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                // 遇到障碍物直接跳过
                if (obstacleGrid[i][j] == 1) {
                    continue;
                }
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

### [64.最小路径和](https://leetcode.cn/problems/minimum-path-sum/)
题解：

- 题意分析：给定一个包含非负整数的 `m x n` 网格 `grid`，求从$(0,0)$位置到$(m-1,n-1)$位置中路径和最小的情况，返回的是最小的路径和。
- 算法思想：选或不选，要么向右，要么向下。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：$dp[i][j]$表示到达(i,j)位置最小的路径和，最终结果为$dp[m-1][n-1]$
   - 递归公式：基于选或不选思考：$dp[i][j]=Math.min(dp[i-1][j],dp[i][j-1]) + grid[i][j]$
   - dp 数组初始化：边界初始化为默认累计值
   - 遍历顺序：从左往右，从上往下
```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int j = 1; j < n; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
}
```

### [5.最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)
题解：

- 题意分析：要求找到原字符串中的最长回文子串
- 这里介绍两种解法：**中心扩散法--适用回文串长度问题的通法 和 动态规划--最长子序列问题的通法**
- 解法一：动态规划：子序列构造二维 dp 数组
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：**布尔类型的**`**dp[i][j]**`**：表示区间范围**`**[i, j]**`** （注意是左闭右闭）的子串是否是回文子串，如果是回文子串 **`**dp[i][j]**`**为**`**true**`**，否则为**`**false**`**。**收集结果是通过每次遍历出现回文串就去更新结果，**因为要返回的子串，还需要保存最长情况下的 i 和 j**
   - 递推公式：先从判断 $s[i]$ 和 $s[j]$ 的相等性开始，不相等为默认的 false，相等则需要根据 i,j 大小关系进一步判断：
      - $j-i\le1$时，说明子串长度为 2 或 1，在已经相等的条件下，一定为回文串，$dp[i][j]$ 为 true
      - $j-i> 1$时，说明子串长度大于 2，则需要再向内判断是否回文也就是 $dp[i+1][j-1]$。
   - dp 数组初始化：还没开始遍历判断，默认所有情况初始化为 false
   - 遍历顺序：**从下往上遍历(i 从 n-1 开始)，从左往右遍历(j 从 i 开始)**
      - 这里的遍历顺序是因为计算 `dp[i][j]` 可能会用到 `dp[i+1][j-1]`（左下角的元素），因此需要从左下往右上遍历才能保证计算`dp[i][j]` 用的是新值
```java
class Solution {
    public String longestPalindrome(String s) {
        char[] chs = s.toCharArray();
        int n = chs.length;
        boolean[][] dp = new boolean[n][n];
        int left = 0, right = 0;
        int max = 0;
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (chs[i] == chs[j] && (j - i <= 1 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;
                }
                if (dp[i][j] && j - i + 1 > max) {
                    max = j - i + 1;
                    left = i;
                    right = j;
                }
            }
        }
        return s.substring(left, right + 1);
    }
}
```

- 解法二：针对回文串问题的解法：中心扩散法，降低空间复杂度至$O(1)$
- 根据回文串轴对称的性质，从某个位置向左右扩展判断，故称中心扩散；注意这里的起始位置有两种情况：
   - 子串奇数长度，从某一点出发；
   - 子串偶数长度，从某个长度为 2 的回文串出发；
- 收集结果：最大长度为上述两种情况中的最大值，题目要求返回具体的子串，因此需要记录开始位置($i- (len-1)/2$) 和结束位置($i+len/2$)
```java
class Solution {
    public String longestPalindrome(String s) {
        char[] chs = s.toCharArray();
        int start = 0, end = 0;
        // 1. 枚举回文串中心点
        for (int i = 0; i < chs.length; i++) {
            // 情况一: 奇数长度
            int len1 = extend(chs, i, i);
            // 情况二: 偶数长度
            int len2 = extend(chs, i, i + 1);
            int len = Math.max(len1, len2);
            // 2. 更新结果
            if (len > end - start) {
                // 统一两种情况长度计算: i 靠左 -> 向下取整
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }

    // 中心扩散法求回文串长度
    private int extend(char[] chs, int left, int right) {
        while (left >= 0 && right <= chs.length - 1 && chs[left] == chs[right]) {
            left--;
            right++;
        }
        return right - left - 1;
    }
}
```

- 同类型的题目：
   - [647.回文子串](https://leetcode.cn/problems/palindromic-substrings/)
   647.回文子串题解：

- 题意分析：给你一个字符串 s ，请你统计并返回这个字符串中 回文子串 的数目。
- 思路：基于中心扩散法，判断成功一次累计一次
```java
class Solution {
    public int countSubstrings(String s) {
        char[] chs = s.toCharArray();
        int ans = 0;
        for (int i = 0; i < chs.length; i++) {
            ans += extend(chs, i, i);
            ans += extend(chs, i, i + 1);
        }
        return ans;
    }

    private int extend(char[] chs, int left, int right) {
        int count = 0;
        while (left >= 0 && right < chs.length && chs[left] == chs[right]) {
            left--;
            right++;
            count++;
        }
        return count;
    }
}
```

### [1143.最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)
题解：

- 题意分析：给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 公共子序列 的长度。如果不存在 公共子序列 ，返回 `0` 。
   - 子序列：不要求字符之间连续，需要保证相对顺序。
   - 公共：要求字符相同。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：$dp[i][j]$ 表示当匹配到字符串 1 的 i 和字符串的 2 位置时公共子串长度，在遍历过程中更新结果
      - **这里注意定义的是 **$(i,j)$**的前面匹配情况，这样处理简化边界条件处理**
   - 递推公式：基于选或不选：
      - 匹配成功，选：$dp[i][j]=dp[i-1][j-1] + 1$
      - 匹配不成功，不选：$dp[i][j]=Math.max(dp[i-1][j],dp[i][j-1])$
   - dp 数组初始化：默认初始化为 0
   - 遍历顺序：从左到右，从上到下
```java
class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int ans = 0;
        char[] chs1 = text1.toCharArray();
        char[] chs2 = text2.toCharArray();
        int len1 = chs1.length;
        int len2 = chs2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                // 选
                if (chs1[i - 1] == chs2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {    // 不选
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
                // 要求最大, 遍历过程中更新
                if (dp[i][j] > ans) {
                    ans = dp[i][j];
                }
            }
        }
        return ans;
    }
}
```

### [72.编辑距离](https://leetcode.cn/problems/edit-distance/)
题解：

- 题意分析：题目要求将 word1 转换成 word2，转换过程中对两个字符串都可以进行增删改操作
   - 本质上两个字符串是相互转换的。
- **将问题转换：找到两个字符串的最长公共序列，这个序列可以通过指定操作两个字符串来得到**。
- 用伪代码将题目要求写出来：
```java
if (word1[i] == word2[j]) {
	什么事不做
}
if (word1[i] != word2[j]) {
	可以增
    可以改
    可以删
    // 三种操作都可以, 但需要用最优的方式
}
```

- 也就是说总体来说存在四种情况。
- 动归五部曲：
   - dp 数组和下标的含义以及收集结果的位置：$dp[i][j]$** 表示下标i-1为结尾的字符串word1，和以下标j-1为结尾的字符串word2，需要进行的操作数**，收集结果的位置在 $dp[len1][len2]$
      - 同样这里设置为$i-1$ 和 $j-1$ 可以避免边界判断和统一初始化过程。
   - 递归公式：基于上述四种情况思考：
      - 相等：什么都不做，$dp[i][j] = dp[i-1][j-1]$
      - 不相等：**再细分情况——增、删是一类（对 word1 删就是对 word2 增），改是一类，那么问题就变成了删 word1/word2，改 word1/word2**
         - 删：$Math.min(dp[i-1][j],dp[i][j-1]) + 1$
         - 改：$dp[i-1][j-1] + 1$
         - 两者再取最小值
   - dp 数组初始化：初始化边界情况即两个字符串各为空时需要进行的操作数。
   - 遍历顺序：从左到右，从上到下
```java
class Solution {
    public int minDistance(String word1, String word2) {
        char[] chs1 = word1.toCharArray();
        char[] chs2 = word2.toCharArray();
        int len1 = chs1.length, len2 = chs2.length;
        int[][] dp = new int[len1 + 1][len2 + 1];
        for (int i = 0; i <= len1; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= len2; j++) {
            dp[0][j] = j;
        }
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (chs1[i - 1] == chs2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]) + 1, dp[i - 1][j - 1] + 1);
                }
            }
        }
        return dp[len1][len2];
    }
}
```

### [97.交错字符串](https://leetcode.cn/problems/interleaving-string/)
题解：

- 题意分析：判断原字符串是不是由给定的两个字符串交错生成的。
   - 这里的约束是两个给定的字符串不能一次用完。
- 基于选或不选思考，一部分 s1 和一部分 s2 能不能匹配上对应长度的 s3。
- 思路：动态规划
   - dp 数组和下标的含义以及收集结果的位置：布尔类型的`dp[i][j]` 表示当到 s1 的 $i-1$ 位置和 s2 的 $j-1$ 位置时是否能与对应的 s3 中的位置匹配上。
   - 状态转移方程：当前位置是否可以匹配上，$f(i,j)=[f(i-1,j)\operatorname{and}s_1(i-1)=s_3(p)]\operatorname{or}[f(i,j-1)\operatorname{and}s_2(j-1)=s_3(p)]$
      - 为什么要 `dp[i][j]` 与匹配结果相与？只要上述判断式中有一个是 true 就算匹配成功，又因计算时并无法连续得到结果，因此设置相与。
   - dp 数组初始化：两个空串匹配结果为 true。
   - 遍历顺序：双重 for 循环。
- 优化：使用滚动数组的方式代替二维数组。
   - 为什么 $dp[len1]$ 可以去掉？
      - 双重 for 循环，外层的 i 在内存循环时是固定的，那么可以直接利用滚动的思想去优化。
```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int len1 = s1.length();
        int len2 = s2.length();
        int len3 = s3.length();
        if (len1 + len2 != len3) {
            return false;
        }
        boolean[][] dp = new boolean[len1 + 1][len2 + 1];
        dp[0][0] = true;
        for (int i = 0; i <= len1; i++) {
            for (int j = 0; j <= len2; j++) {
                // 与 s3 做匹配
                int index = i + j - 1;
                if (i > 0) {
                    dp[i][j] = dp[i - 1][j] && s1.charAt(i - 1) == s3.charAt(index);
                }
                if (j > 0) {
                    // 只要有一个字串匹配成功即可
                    dp[i][j] = dp[i][j] || (dp[i][j - 1] && s2.charAt(j - 1) == s3.charAt(index));
                }
            }
        }
        return dp[len1][len2];
    }

    // 优化：使用滚动数组优化空间复杂度
    public boolean isInterleave(String s1, String s2, String s3) {
        int len1 = s1.length();
        int len2 = s2.length();
        int len3 = s3.length();
        if (len1 + len2 != len3) {
            return false;
        }
        boolean[] dp = new boolean[len2 + 1];
        dp[0] = true;
        for (int i = 0; i <= len1; i++) {
            for (int j = 0; j <= len2; j++) {
                int index = i + j - 1;
                if (i > 0) {
                    dp[j] = dp[j] && (s1.charAt(i - 1) == s3.charAt(index));
                }
                if (j > 0) {
                    dp[j] = dp[j] || (dp[j - 1] && s2.charAt(j - 1) == s3.charAt(index));
                }
            }
        }
        return dp[len2];
    }
}
```

### [115.不同的子序列](https://leetcode.cn/problems/distinct-subsequences/)
题解：

- 题意分析：给你两个字符串 s 和 t ，统计并返回在 s 的 子序列 中 t 出现的个数；这里的子序列规定为字母出现次数和顺序相同。
   - t 相当于 s 中通过删减得到的子串，考虑的是方案数。
- 题解：动态规划，基于选或不选去思考
   - dp 数组和下标的含义以及收集结果的位置：$dp[i]][j]$表示 s 串 i 索引之前出现在 t 串 j 索引之前的子序列出现的次数。结果为$dp[n][m]$
   - 状态转移方程：$dp[i][j] = \begin{cases}
      dp[i - 1][j - 1] + dp[i - 1][j]; & 可以匹配 \\
      dp[i - 1][j] & 无法匹配
      \end{cases}$
   - dp 数组初始化：需要初始化边界部分
   - 遍历顺序：根据递推公式可以看出来从左往右从上往下遍历
- 可以进一步优化的方式：从 dp 数组的样子中就可以看出可以优化成一维数组：i 的状态从 i-1 位置的值计算的来，可以去掉这部分空间占用。
- ![image.png](https://cdn.nlark.com/yuque/0/2024/png/40499251/1707368215743-4bc2a4e7-9521-4403-a1da-c29b7919a3c3.png#averageHue=%23efeded&clientId=u069c764c-8eb3-4&from=paste&height=452&id=HVbD0&originHeight=565&originWidth=563&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=43179&status=done&style=none&taskId=ub61bfdba-92fc-4128-b8ff-68b2e61643e&title=&width=450.4)
- 思路：改 dp 数组为一维滚动数组，遍历顺序改为从右向左遍历，因为改为滚动后如果还从左往右算，每次计算会把左边的值覆盖掉，这样算不到正确的。
```java
class Solution {
    public static final int MOD = (int) 1e9 + 7;

    public int numDistinct(String s, String t) {
        char[] chs = s.toCharArray();
        char[] cht = t.toCharArray();
        int lens = chs.length;
        int lent = cht.length;
        int[][] dp = new int[lens + 1][lent + 1];
        // 初始化: t 为空, s 中的字符只有全部删除才符合
        for (int i = 0; i <= lens; i++) {
            dp[i][0] = 1;
        }
        // 初始化: s 为空, 无论如何处理都不符合
        for (int j = 0; j <= lent; j++) {
            dp[0][j] = 0;
        }
        // 初始化: s 和 t 都为空, 默认为符合
        dp[0][0] = 1;
        for (int i = 1; i <= lens; i++) {
            for (int j = 1; j <= lent; j++) {
                // 选
                if (chs[i - 1] == cht[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {    // 不选
                    dp[i][j] = dp[i - 1][j];
                }
                dp[i][j] %= MOD;
            }
        }
        return dp[lens][lent] % MOD;
    }

    // 优化: 改为滚动数组
    public int numDistinct(String s, String t) {
        char[] chs = s.toCharArray();
        char[] cht = t.toCharArray();
        int lens = chs.length;
        int lent = cht.length;
        int[] dp = new int[lent + 1];
        dp[lent] = 1;
        for (int i = lens - 1; i >= 0; i--) {
            for (int j = 0; j < lent; j++) {
                if (chs[i] == cht[j]) {
                    dp[j] += dp[j + 1];
                }
            }
        }
        return dp[0];
    }
}
```

### [120.三角形最小路径和](https://leetcode.cn/problems/triangle/)
题解：

- 题意分析：给定一个三角形 `triangle` ，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。
   - 三角形在题目中大小已经被固定，就是一个杨辉三角类似的形状。$j \le i$
- 转换下题意：
```java
[2],
[3,4],
[6,5,7],
[4,1,8,3]
相邻结点：与 (i, j) 点相邻的结点为 (i + 1, j) 和 (i + 1, j + 1)。
```

- 从贪心角度思考，应该从下向上遍历，因为下层元素多，可以逐渐从局部最小扩展到全局最小。
- 思路：动态规划——基于选或不选确定递推公式。
   - dp 数组及其下标含义：dp[i][j] 表示从底边到$(i,j)$位置的最小路径和，最终结果为 dp[0][0]
   - 状态转移方程：$dp[i][j]=Math.min(dp[i+1][j], dp[i+1][j+1])+triangle[i][j]$
   - dp 数组初始化：底边初始化为 0
   - 遍历顺序：从递推公式和数组定义就可以看出，需要从下向上遍历
- 注意空间优化：遍历过程中 i 只是起到控制层数的作用，可以直接去掉 dp 数组中的此部分
```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[][] dp = new int[n + 1][n + 1];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[i][j] = Math.min(dp[i + 1][j], dp[i + 1][j + 1]) + triangle.get(i).get(j);
            }
        }
        return dp[0][0];
    }
    // 空间优化
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] dp = new int[n + 1];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[j] = Math.min(dp[j], dp[j + 1]) + triangle.get(i).get(j);
            }
        }
        return dp[0];
    }
}
```

### [873.最长的斐波那契子序列的长度](https://leetcode.cn/problems/length-of-longest-fibonacci-subsequence/)
题解：

- 题意分析：给定一个严格递增的正整数数组形成序列 arr ，找到 arr 中最长的斐波那契式的子序列的长度。如果一个不存在，返回  0 。
   - 斐波那契式要求：$n \ge 3$；对于所有 `i + 2 <= n`，都有$X_i + X_{i + 1} = X_{i + 2}$
   - 子序列要求：从原序列中删除元素的同时保持元素顺序不变。
- 显然确定一个斐波那契式需要一个三元组，同时又是求子序列相关的问题，因此想到动态规划
- 思路：二维动态规划：
   - dp 数组及其下标含义：
   - 状态转移方程：
   - dp 数组初始化：
   - 遍历顺序：
```java
class Solution {
    public int lenLongestFibSubseq(int[] arr) {
        Map<Integer, Integer> indices = new HashMap<Integer, Integer>();
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            indices.put(arr[i], i);
        }
        int[][] dp = new int[n][n];
        int ans = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i - 1; j >= 0 && arr[j] * 2 > arr[i]; j--) {
                int k = indices.getOrDefault(arr[i] - arr[j], -1);
                if (k >= 0) {
                    dp[j][i] = Math.max(dp[k][j] + 1, 3);
                }
                ans = Math.max(ans, dp[j][i]);
            }
        }
        return ans;
    }
}
```

# 技巧
### [169.多数元素](https://leetcode.cn/problems/majority-element/)
题解：

- 题意分析：题目要求返回出现次数超过一半的元素
- 最优解法：摩尔投票法
   - 算法思想的核心是票数相互抵消
```java
class Solution {
    public int majorityElement(int[] nums) {
        int votes = 0, x = 0;
        for (int num : nums) {
            if (votes == 0) {
                x = num;
            }
            votes += num == x ? 1 : -1;
        }
        return x;
    }
}
```

- 同类型的题目：
   - [229. 多数元素 II](https://leetcode.cn/problems/majority-element-ii/)
229. 多数元素 II答：

- 题意分析：本题与上题的区别在于修改了比较的数值为$\lfloor n/3 \rfloor$
- 可以直接通过枚举完成，空间复杂度不符合要求。
```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int x : nums) {
            // if (map.containsKey(x)) {
            //     map.put(x, map.get(x) + 1);
            // } else {
            //     map.put(x , 1);
            // }
            map.merge(x, 1, Integer::sum);	// 替换
        }
        int k = nums.length / 3;
        List<Integer> ans = new ArrayList<>();
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (entry.getValue() > k) {
                ans.add(entry.getKey());
            }
        }
        return ans;
    }
}
```

- 思考类似于上题的解法：摩尔投票法的推广版$n/k$的情况
- 可以预先确定的信息：
   - 


### [31. 下一个排列](https://leetcode.cn/problems/next-permutation/)
题解：

- 题意：给定若干个数字，将其组合为一个整数。如何将这些数字重新排列，以得到下一个更大的整数。如 123 下一个更大的数为 132。如果没有更大的整数，则输出最小的整数。
- 从示例思考得到结果：数字 1，2，3 组成的字典序中最小 123，接着是 132，213，231，312，321
- 要求：大数要向前交换，难点在于此：**不光大数向前交换就满足条件，增加的幅度还不能大 **
   - **在尽可能靠右的低位进行交换**——**需要从后向前查找**
   - **将一个尽可能小的大数与前面的小数交换**——**相邻查找**
   - **将大数换到前面后，需要将大数后面的所有数 重置为升序**，**升序排列就是最小的排列**。
- 算法流程： 
   - 从后向前查找：找出**相邻的第一个**大数大于小数的情况，**i-1为交换的位置；**
   - 对`[i,len)`的位置进行**升序排序**，排序后再对**第一个**大于`i-1`的元素进行交换即可。
   - 处理特殊情况即**数组为降序数组**：直接排序后返回。
```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        // 从后向前查找
        for (int i = n - 1; i > 0; i--) {
            // i - 1 为交换位置
            if (nums[i] > nums[i - 1]) {
                // 排序后换的就是相对小的大数了
                Arrays.sort(nums, i, n);
                // 找到第一个大于交换元素的位置即可
                for (int j = i; j < n; j++) {
                    if (nums[j] > nums[i - 1]) {
                        int temp = nums[j];
                        nums[j] = nums[i - 1];
                        nums[i - 1] = temp;
                        return;
                    }
                }
            }
        }
        // 处理原数组顺序最大的情况也就是降序数组
        Arrays.sort(nums);
    }
}
```

- 同类型的题目：



### [287.寻找重复数](https://leetcode.cn/problems/find-the-duplicate-number/)
题解：

- 题意分析：题目中有几个关键条件：
   - 数组长度为 n+1，而数组的元素大小满足 $1\le nums[i] \le n$
   - 上条件可以直接得出数组必定存在重复元素。
   - 提前假设数组中就存在一个重复整数。
- 最优解法：时间复杂度$O(n)$;空间复杂度$O(1)$

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int fast = 0, slow = 0;
        while (true) {
            fast = nums[nums[fast]];
            slow = nums[slow];
            if (slow == fast) {
                fast = 0;
                while (nums[slow] != nums[fast]) {
                    fast = nums[fast];
                    slow = nums[slow];
                }
                return nums[slow];
            }
        }
    }
}
```


### [152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)
题解：

- 题意分析：给你一个整数数组 nums ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。
- 算法思想：直接通过枚举每次数组元素作为起点的子数组乘积。
   - 大量重复计算。需要优化
- 方法二：动态规划：要求连续子数组，基本的dp公式为$dp[i]=Math.max(nums[i],dp[i-1]*nums[i])$
- 方法三：基于贪心思想思考：
   - 因为数组存在负数，如果负数为偶数时，负数的累计有益，为奇数，则累计有害
   - 基于上述约束，需要遍历过程中保存两类值：乘积最大的负值和乘积最大的正值。
- 遍历数组时计算当前乘积的最大值，不断更新
- 令$imax$为当前最大值，则当前最大值为$imax = max(imax * nums[i], nums[i])$
- 由于存在负数，那么会导致最大的变最小的，最小的变最大的。因此还需要维护当前乘积最小值$imin$，$imin = min(imin * nums[i], nums[i])$
- **_当负数出现时则imax与imin进行交换再进行下一步计算_**
- 结果就是$res=Math.max(res,imax)$
```java
class Solution {
    public int maxProduct(int[] nums) {
        int res = Integer.MIN_VALUE;
        // 阶段最大值
        int max = 1;
        // 阶段最小值
        int min = 1;
        for (int i = 0; i < nums.length; i++) {
            // 当遇到负数的时候进行交换，因为阶段最小值*负数就变阶段最大了，反之同理
            if (nums[i] < 0) {
                int temp = max;
                max = min;
                min = temp;
            }
            max = Math.max(nums[i] * max, nums[i]);
            min = Math.min(nums[i] * min, nums[i]);
            res = Math.max(max, res);
        }
        return res;
    }
}
```

# 数学和位运算
### [29.两数相除](https://leetcode.cn/problems/divide-two-integers/)
题解：

- 题意分析：使用加法模拟整数除法（向下截断）。注意限制条件	
   - 1. 不能使用 long 型来存储中间变量
   - 2. 商等于或超出边界，则根据符号返回 Integer.MAX_VALUE 或 Integer.MIN_VALUE
- 算法思想：使用的是一种倍增思想（类似慢开始）
- 算法流程：
   - 先对边界条件进行判断
   - 记录最终结果的符号，并将两数都映射为负数
   - 采用倍增法得到结果。
```java
class Solution {
    // Math.abs(MIN) - MAX = 1
    int MIN = Integer.MIN_VALUE, MAX = Integer.MAX_VALUE;
    // 用于预先判断结果是否溢出
    int LIMIT = MIN / 2;

    public int divide(int dividend, int divisor) {
        // 1. 处理各自边界, 同时映射到负数空间以防止后续计算溢出
        // 处理溢出情况
        if (dividend == MIN && divisor == -1) {
            return MAX;
        }
        // 因为后续会统一处理为负数,因此这里提前判断结果是否为负数
        boolean flag = false;
        if ((dividend > 0 && divisor < 0) || (dividend < 0 && divisor > 0)) {
            flag = true;
        }
        // 将正数转为负数进行操作, 增大表示范围
        if (dividend > 0) {
            dividend = -dividend;
        }
        if (divisor > 0) {
            divisor = -divisor;
        }

        // 2. 基于倍增法来实现除法
        int ans = 0;
        // 注意整个参与计算的数值都是负数
        while (dividend <= divisor) {
            int a = divisor, b = -1;
            // "慢开始"
            while (a >= LIMIT && b >= LIMIT && a + a >= dividend) {
                a += a;
                b += b;
            }
            dividend -= a;
            ans += b;
        }
        return flag ? ans : -ans;
    }
}
```

- 记录下本题使用的倍增法板子：
```java
public int divide(int a, int b) {
    int ans = 0; // 最终的商
    while(a >= b) {
        int d = b, c = 1; // 当前倍增的部分商
        while(d <= Integer.MAX_VALUE >> 1 && d + d <= a) {
            d += d; // 除数倍增
            c += c; // 当前商倍增
        } 
        a -= d; // a剩余部分
        ans += c; // 累计商
    }
    return ans;
}
```

### [67.二进制求和](https://leetcode.cn/problems/add-binary/)
题解：

- 题意分析：给定两个二进制字符串，返回其和的二进制字符串
- 基本思想：对于这种构造数字结果的字符串，采用**对字符串先处理后反转**的思想。
- 用一个变量保存进位即可，而对于其中长度不足的位置直接以 0 加上。
```java
class Solution {
    public String addBinary(String a, String b) {
        char[] numa = a.toCharArray();
        char[] numb = b.toCharArray();
        int n = Math.max(a.length(), b.length());
        StringBuilder ans = new StringBuilder();
        int carry = 0;
        for (int i = 0; i < n; i++) {
            carry += i < a.length() ? numa[a.length() - 1 - i] - '0' : 0;
            carry += i < b.length() ? numb[b.length() - 1 - i] - '0' : 0;
            ans.append((char) (carry % 2 + '0'));
            carry /= 2;
        }
        if (carry > 0) {
            ans.append('1');
        }
        return ans.reverse().toString();
    }
}
```

- 同类型的题目：[445.两数相加 II](https://leetcode.cn/problems/add-two-numbers-ii/)

### [338.比特位计数](https://leetcode.cn/problems/counting-bits/)
题解：

- 题意分析：填充长度为$n + 1$数组的内容，要求元素值为下标二进制表示下 1 的个数。
- 思路一：边遍历数组边依次填充。
   - 关键步骤在于：$x=x\&(x-1)$操作可以将 x 的二进制表示的最后一个 1 变为 0
```java
class Solution {
    public int[] countBits(int n) {
        int[] ans = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            ans[i] = countOne(i);
        }
        return ans;
    }

    // 计算当前数字的1的个数
    private int countOne(int num) {
        int ones = 0;
        while (num > 0) {
            num &= (num - 1);
            ones++;
        }
        return ones;
    }
}
```

- 上述思路很明显可以优化，存在着重复计算，后续统计可以利用前面的计算结果，很容易想到思路二动态规划
- 思路二：动态规划
   - 对上述思路优化就是使用结果数组作为 dp 数组
   - 如何利用之前的结果：利用左移操作末位添 0 的思想，需要注意统一奇偶两种情况（奇数除法会向下截断，因此统计时需要额外添加 1）
```java
class Solution {
    public int[] countBits(int n) {
        int[] ans = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            ans[i] = ans[i >> 1] + (i & 1);
        }
        return ans;
    }
}
```

### [136.只出现一次的数字](https://leetcode.cn/problems/single-number/)
题解：

- 异或运算的一些特殊性质：相同的数异或得到 0；任何数与 0 异或得到本身。
```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = nums[0];
        if (nums.length > 1) {
            for (int i = 1; i < nums.length; i++) {
                res = res ^ nums[i];
            }
        }
        return res;
    }
}
```

- 同类型的题目：
   - [137. 只出现一次的数字 II](https://leetcode.cn/problems/single-number-ii/)
   - [260. 只出现一次的数字 III](https://leetcode.cn/problems/single-number-iii/)
137. 只出现一次的数字 II题解：

- 题意分析：题目改为了除了一个单独元素其他的都出现了三次。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            int cnt1 = 0;
            for (int x : nums) {
                cnt1 += x >> i & 1;
            }
            ans |= cnt1 % 3 << i;
        }
        return ans;
    }
}
```
260. 只出现一次的数字 III

### [318.最大单词长度乘积](https://leetcode.cn/problems/maximum-product-of-word-lengths/)
题解：

- 题意分析：找出并返回 `length(words[i]) * length(words[j])` 的最大值，并且这两个单词不含有公共字母。如果不存在这样的两个单词，返回 0 
   - 单词不含有公共字母
   - 要返回可能存在的情况中的最大值
- 思路：根据题意进行模拟即可，利用每个$word[i]$只有小写字母，且只需要区分两字符是否有字母重复。
   - **利用掩码思想简化字符重复判断：**
      - 具体点说：使用一个 `int`来代指某个 `word[i]`：低 26 来代指 a-z 是否出现过
      - 然后对每个字符对所对应的两个 `int`值执行`&`操作（即若两字符无重复字符，则结果为 0），并得出最终答案。
```java
class Solution {
    public int maxProduct(String[] words) {
        int ans = 0;
        int n = words.length;
        int[] masks = new int[n];
        int index = 0;
        for (String word : words) {
            int temp = 0;
            for (int i = 0; i < word.length(); i++) {
                int count = word.charAt(i) - 'a';
                // 计算当前字母对应的掩码
                int mask = 1 << count;
                // 汇总每个字母的掩码
                temp |= mask;
            }
            masks[index++] = temp;
        }
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                // 判断字母是否重复
                if ((masks[i] & masks[j]) == 0) {
                    ans = Math.max(ans, words[i].length() * words[j].length());
                }
            }
        }
        return ans;
    }
}
```

### [421.数组中两个数的最大异或值](https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/)
题解：

- 题意分析：返回数组中两个数的异或运算的最大结果
- 分析思路：将数组的数转成二进制，因为要求得到最大结果，可以尝试从二进制左边第一位开始去枚举可能的情况，因为每轮枚举确定了可能的最大值，下一轮需要枚举的位置是减少的，整体的时间复杂度是小于$O(n^2)$
- 算法流程：从最高位开始枚举，每次枚举的目标是判断两数（两个数的当前位）异或是否为 1（实现的思路是两数之和）
- 代码细节：
   - 位运算：
```java
class Solution {
    public int findMaximumXOR(int[] nums) {
        int max = 0;
        int n = nums.length;
        for (int x : nums) {
            max = Math.max(x, max);
        }
        int leftBit = 31 - Integer.numberOfLeadingZeros(max);
        int ans = 0, mask = 0;
        Set<Integer> seen = new HashSet<>();
        for (int i = leftBit; i >= 0; i--) {
            seen.clear();
            mask |= 1 << i;
            int temp = ans | (1 << i);
            for (int x : nums) {
                x &= mask;
                if (seen.contains(temp ^ x)) {
                    ans = temp;
                    break;
                }
                seen.add(x);
            }
        }
        return ans;
    }
}
```

### [50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)
题解：

- 题意分析：自己实现一个计算$x^n$的方式
- 快速幂板子题：快速幂+迭代方式
- 快速幂算法的本质是分治算法，思考两种情况：
   - 计算$x^{64}$:计算方式可以按照二分思想处理$x \rightarrow x^2 \rightarrow x^{4} \rightarrow x^{8} \dots \rightarrow x ^{32} \rightarrow x ^ {64}$，时间复杂度可以降至$O(logn)$
   - 计算$x^{77}$:计算方式同上，但需要注意奇数情况：
      - 要计算$x^n$，先算$y=x^{\frac{n}{2}}$，根据递归结果，n为偶数时，$x^n=y^2$；n为奇数时，$\frac{n}{2}$向下取整，$x^n = y^2 * x$。
- 本题需要注意 n 可能会为$-2^{31} <= n <= 2^{31}-1$，对于 n 为负数的情况需要单独处理。
```java
class Solution {
    public double myPow(double x, int n) {
        // 当 n = -2^31 时 -n 会溢出导致错误, 使用 long 类型保存或者取绝对值
        long N = n;
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }

    private double quickMul(double x, long n) {
        double ans = 1.0;
        double temp = x;
        while (n > 0) {
            if (n % 2 == 1) {
                ans *= temp;
            }
            temp *= temp;
            n /= 2;
        }
        return ans;
    }
}
```

- 同类型的题目：
   - [372. 超级次方](https://leetcode.cn/problems/super-pow/)
372. 超级次方题解：

```java
class Solution {
    int MOD = 1337;

    public int superPow(int a, int[] b) {
        return dfs(a, b, b.length - 1);
    }

    int dfs(int a, int[] b, int u) {
        if (u == -1) {
            return 1;
        }
        return pow(dfs(a, b, u - 1), 10) * pow(a, b[u]) % MOD;
    }

    int pow(int a, int b) {
        int ans = 1;
        a %= MOD;
        while (b-- > 0) {
            ans = ans * a % MOD;
        }
        return ans;
    }
}
```
