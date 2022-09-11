#### 一、归并排序：
__## 归并排序基础：__
* 归并排序的基础是将两个有序数列归并成一个有序数列，这是归并排序的基础，整个归并排序都是围绕这一基础实现的。
[![OoZZBF.jpg](https://s1.ax1x.com/2022/05/18/OoZZBF.jpg)](https://imgtu.com/i/OoZZBF)
* 将上述两个有序数列归并成一个有序数列，设置一个数组A中的驱动i，设置一个数组B中的驱动j，再设置一个数组Sort中的驱动k，由于我们对数组元素排序，所以不需要将这三个驱动设置成为指针，只需要设置成为整数就好。
* 首先将i和j比较：如果j对应的元素小于i对应的元素，将j对应的元素放入Sort中k对应的位置，并且将j的位置++，k的位置++；如果j对应的元素大于i对应的元素，将i对应的元素放入Sort中k对应的位置，并且将i的位置++，k的位置++。
* 如果一个数组全部检测完毕，则接下来不需要对另一个数组进行排序了，只需要将另一个数组中的剩余元素按顺序放到Sort中即可，比如数组A检测完毕，此时i应该等于A中元素个数，接下来把B中的元素从j对应的位置依次放进Sort中即可。
__## 我们先来实现一下这个代码：__
```C
void merge_sort(int* a , int*b , int length_a , int length_b)
{
	int i = 0;
	int j = 0;
	int k = 0;
	int* sort  = (int*)malloc(sizeof(int) * (length_a + length_b));
	if (!sort)
	{
		fprintf(stdout,"malloc apply failed!");
		exit(1);
	} 
	//上面的代码创建了一个sort数组，是排序后的新数组，同时创建了i，j，k三个驱动。
	while (i < length_a && j < length_b)
	{
		if (a[i] < b[j])
			sort[k++] = a[i++];
		else
			sort[k++] = b[j++];
	}
	//按照归并的思路进行排序。
	while (i < length_a)
		sort[k++] = a[i++];
	while (j < length_b)
		sort[k++] = b[j++];
	//如果有一个数组检测完毕，那么我们就把另外一个数组剩下的元素直接放在sort后面。
	for (int i = 0 ; i < length_a + length_b ; i++)
		printf("%d ",sort[i]);
	printf("\n");
	//将sort数组打印出来。
	return;
}
```

__## 接下来我们研究一个数组中的归并排序思路：__
[![OoGDv4.jpg](https://s1.ax1x.com/2022/05/18/OoGDv4.jpg)](https://imgtu.com/i/OoGDv4)
* 对于这样的一个数组，由两个有序数列构成，我们要将其排序成一个有序数列，思想和刚才相同
* 我们构建一个函数merge来完成归并排序，首相向该函数传递四个参数：第一个参数是array数组，第二个参数是第一个有序数列的起始位置，第三个参数是第二个有序数列的起始位置，第四个参数是第二个有序数列的末位置，通过传递这些参数我们就能直到这两个有序数列的长度，就能将他们放在两个数组中，从而进行归并排序。
（通常我们也把第一个有序数列叫做左序列，第二个有序数列叫做右序列）
```C
void merge(int* array , int L_start , int R_start , int R_end)
{
	int left_size  = R_start - L_start;
	int right_size = R_end - R_start + 1;
	int* Left = (int*)malloc(sizeof(int) * left_size);
	int* Right = (int*)malloc(sizeof(int) * right_size);
	if (!Left && !Right)
	{
		fprintf(stdout , "malloc apply failed!\n");
		exit(1);
	}
	//通过传递进来的参数得到左有序数列的长度和右有序数列的长度，并且创建好两个数组，用来接收这两个有序数列。
	int L = L_start;
	for (int i = 0 ; i < left_size ; i++ , L++)
		Left[i] = array[L];
	int R = R_start;
	for (int i = 0 ; i < right_size ; i++ , R++)
		Right[i] = array[R];
	//创建两个变量L和R，这两个变量用于将array中的这两个有序数列传递出去。
	int K = L_start;//让K等于L_start,K作为sort(array)数组的驱动。
	L = 0;
	R = 0;
	while (L < left_size && R < right_size)
	{
		if (Left[L] < Right[R])
			array[K++] = Left[L++];
		else
			array[K++] = Right[R++];
	}
	while (L < left_size)
		array[K++] = Left[L++];
	while (R < right_size)
		array[K++] = Right[R++];
	//将两个有序数列归并成一个有序数列。
	free(Right);
	free(Left);
	Right = Left = NULL;
	//释放动态内存并且避免野指针。
	for (int i = 0 ; i < left_size + right_size ; i++)
		printf("%d  ",array[i]);
	printf("\n");
	return;
}
```

__## 使用归并排序对一个无序数组进行排序：__
* 对于一个无序数组，我们可以通过这样的思路进行排序：
* 首先，把这个数组二分，分成两个部分A，B，然后在对A和B进行二分，以此类推，直到分出的部分只有一个元素，我们认为一个元素的数组是一个有序数组，因此我们就找到了有序数组，将这两个只有一个元素的数组进行归并排序，形成有序数组，然后再返回到上一级，再把刚刚形成的两个有序数组进行归并排序.........
* 最后我们就能的到一个有序数列。
* 因此除了上面写好的排序函数，我们还需要不断地进行分治，分治的过程应该采用递归，这里我们创建一个函数merge_sort。
```C
void Merge(int* array , int L_start , int R_start , int R_end)
{
	int K = L_start;
	int left_size  = R_start - L_start;
	int right_size = R_end - R_start + 1;
	int* Left = (int*)malloc(sizeof(int) * left_size);
	int* Right = (int*)malloc(sizeof(int) * right_size);
	if (!Left && !Right)
	{
		fprintf(stdout , "malloc apply failed!\n");
		exit(1);
	}
	
	int L = L_start;
	for (int i = 0 ; i < left_size ; i++ , L++)
		Left[i] = array[L];
	int R = R_start;
	for (int i = 0 ; i < right_size ; i++ , R++)
		Right[i] = array[R];
	
	L = 0;
	R = 0;
	while (L < left_size && R < right_size)
	{
		if (Left[L] < Right[R])
			array[K++] = Left[L++];
		else
			array[K++] = Right[R++];
	}
	while (L < left_size)
		array[K++] = Left[L++];
	while (R < right_size)
		array[K++] = Right[R++];
	
	return;
}
void Merge_sort(int* array , int L_start , int R_end)
{
	if (L_start == R_end)
		return;
	//如果进入到的那一层处理的数列只有一个元素，也就是L_strat == R_end，那么就开始回溯。
	int R_start = (L_start + R_end + 1) / 2;
	//在这个函数中，我们采用的分治点是R_start,这个分治点我们可以任意选择，当你选择了不同的分治点，下面的两个递归也需要进行相应的改变，第一个递归处理的是当前层的数列分治后的第一个序列的起始点和末位点，第二个递归处理的是当前层的数列分治后的第二个序列的起始点和末位点。
	Merge_sort(array , L_start , R_start - 1);
	Merge_sort(array , R_start , R_end);
	Merge(array , L_start , R_start , R_end);
	//递归调用两次函数之后就对当前层的数列的两个子序列完成了排序，然后在归并这两个子序列。
	return;
}
```