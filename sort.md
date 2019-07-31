#### 1.冒泡排序

> 思路
>
> 冒泡排序只会操作相邻的两个数据。
>
> 每次冒泡操作都会对相邻的两个元素进行比较，看是否满足大小关系要求。如果不满足就让它俩互换。
>
> 一次冒泡会让至少一个元素移动到它应该在的位置，重复 n 次，就完成了 n 个数据的排序工作。

```
// 冒泡排序（未优化）
const bubbleSort = arr => {
	console.time('改进前冒泡排序耗时');
	const length = arr.length;
	if (length <= 1) return;
	// i < length - 1 是因为外层只需要 length-1 次就排好了，第 length 次比较是多余的。
	for (let i = 0; i < length - 1; i++) {
		// j < length - i - 1 是因为内层的 length-i-1 到 length-1 的位置已经排好了，不需要再比较一次。
		for (let j = 0; j < length - i - 1; j++) {
			if (arr[j] > arr[j + 1]) {
				const temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
			}
		}
	}
	console.log('改进前 arr :', arr);
	console.timeEnd('改进前冒泡排序耗时');
};
```

> 优化：当某次冒泡操作已经没有数据交换时，说明已经达到完全有序，不用再继续执行后续的冒泡操作。

```
// 冒泡排序（已优化）
const bubbleSort2 = arr => {
	console.time('改进后冒泡排序耗时');
	const length = arr.length;
	if (length <= 1) return;
	// i < length - 1 是因为外层只需要 length-1 次就排好了，第 length 次比较是多余的。
	for (let i = 0; i < length - 1; i++) {
		let hasChange = false; // 提前退出冒泡循环的标志位
		// j < length - i - 1 是因为内层的 length-i-1 到 length-1 的位置已经排好了，不需要再比较一次。
		for (let j = 0; j < length - i - 1; j++) {
			if (arr[j] > arr[j + 1]) {
				const temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
				hasChange = true; // 表示有数据交换
			}
		}

		if (!hasChange) break; // 如果 false 说明所有元素已经到位，没有数据交换，提前退出
	}
	console.log('改进后 arr :', arr);
	console.timeEnd('改进后冒泡排序耗时');
};
```

#### 2.插入排序

> 思路
>
> 一般人打扑克牌，整理牌的时候，都是按牌的大小（从小到大或者从大到小）整理牌的，那每摸一张新牌，就扫描自己的牌，把新牌插入到相应的位置。
>
> 插入排序的工作原理：通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

> 步骤
>
> 从第一个元素开始，该元素可以认为已经被排序；
>
> 取出下一个元素，在已经排序的元素序列中从后向前扫描；
>
> 如果该元素（已排序）大于新元素，将该元素移到下一位置；
>
> 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置；
>
> 将新元素插入到该位置后；
>
> 重复步骤 2~5。

```
// 插入排序
const insertionSort = array => {
	const len = array.length;
	if (len <= 1) return

	let preIndex, current;
	for (let i = 1; i < len; i++) {
		preIndex = i - 1; //待比较元素的下标
		current = array[i]; //当前元素
		while (preIndex >= 0 && array[preIndex] > current) {
			//前置条件之一: 待比较元素比当前元素大
			array[preIndex + 1] = array[preIndex]; //将待比较元素后移一位
			preIndex--; //游标前移一位
		}
		if (preIndex + 1 != i) {
			//避免同一个元素赋值给自身
			array[preIndex + 1] = current; //将当前元素插入预留空位
			console.log('array :', array);
		}
	}
	return array;
};
```

#### 3.选择排序

> 思路
>
> 选择排序算法的实现思路有点类似插入排序，也分已排序区间和未排序区间。但是选择排序每次会从未排序区间中找到最小的元素，将其放到已排序区间的末尾。

> 步骤
>
> 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。
>
> 再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
>
> 重复第二步，直到所有元素均排序完毕。

---
