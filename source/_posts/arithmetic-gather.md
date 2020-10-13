---
title: 给出一个区间的集合，请合并所有重叠的区间。
date: 2020-07-02
categories: [前端面试题,面试题汇总]
tags: 前端面试
---



输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

```javascript
let arr = [
        [1, 3],
        [2, 6],
        [8, 10],
        [15, 18],
      ];

        arr = arr.sort((a, b) => a[0] - b[0]);
        for (let i = 0; i < arr.length - 1; i++) {
          const a2 = arr[i][1],
            b1 = arr[i + 1][0],
            b2 = arr[i + 1][1];
          if (a2 >= b1) {
            arr[i][1] = a2 > b2 ? a2 : b2;
            arr.splice(i + 1, 1);
            i--;
          }
        }
      console.log(arr)

      // let tempMin = [];
      // let tempMax = [];
      // let temp = [];
      // let item = arr.map((data) => {
      //   let min = Math.min(...data);
      //   tempMin.push(min);
      //   let max = Math.max(...data);
      //   tempMax.push(max);
      // });
      // for (let i = 0; i < tempMin.length; i++) {
      //   if (tempMin[i + 1] <= tempMax[i]) {
      //     tempMin.splice(tempMin[i], 1);
      //     tempMax.splice(tempMax[i-1], 1);
      //     i--;
      //   }
      // }
      // tempMin.map((data, index) => {
      //   temp.push([data, tempMax[index]]);
      // });
      // console.log(temp);

      //仅匹配没有连续相同项的数组
      // let all = []
      // let show = false
      // for(let i = 0; i < arr.length; i ++){
      //   if(show) {
      //     show = false
      //     continue
      //   }
      //   if(arr[i+1] && arr[i][1] >= arr[i+1][0]) {
      //     all.push([arr[i][0], arr[i+1][1]])
      //     show = true
      //     continue
      //   }
      //   all.push(arr[i])
      // }
      // console.log(all)
```

