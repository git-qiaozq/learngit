## NGINX学习笔记 - 红黑树
@(joe_e 的笔记本)[A13. Nginx]
### 二叉查找树
-----------
**二叉查找树***(Binary Search Tree)*也称二叉搜索树、有序二叉树，排序二叉树，是指一棵空树或者具有下列性质的[二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%8F%89%E6%A0%91)：
- 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
- 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
- 任意节点的左、右子树也分别为二叉查找树；
- 没有键值相等的节点。

二叉查找树拥有**O(log n)**的时间复杂度。
#### 二叉搜索树的查找算法
在二叉搜索树b中查找x的过程为：
- 若b是空树，则搜索失败，否则：
- 若x等于b的根节点的数据域之值，则查找成功；否则：
- 若x小于b的根节点的数据域之值，则搜索左子树；否则：
- 查找右子树。

```cpp
#include <stdio.h>

typedef struct bstree_s bstree_t;
typedef struct bstree_s {
    int          key;
    void        *data;
    bstree_t    *left;
    bstree_t    *right;
};

/* 从tree节点开始查找key，p为未找到对应节点时的最后一级节点，r为找到key对应的节点 */
int bstree_search(bstree_t *tree, int key, bstree_t *p, bstree_t **r)
{
    /* 查找的当前节点为NULL，表示已经查找到叶子节点，但是没找到匹配值 */
    if (NULL == tree) {
        *r = p;
        return false;
    }

    /* 查找成功 */
    if (key == tree->key) {
        *r = tree;
        return true;

    /* 在左子树中继续查找 */
    } else if (key < tree->left->key) {
        bstree_search(tree->left, key, tree, r);

    /* 在右子树中继续查找 */
    } else {
        bstree_search(tree->right, key, tree, r);
    }
}
```
#### 在二叉搜索树插入节点的算法
向一个二叉搜索树b中插入一个节点s的算法，过程为：
- 若b是空树，则将s所指结点作为根节点插入，否则：
- 若s->key等于b的根节点的数据域之值，则报错返回，否则：
- 若s->key小于b的根节点的数据域之值，则把s所指节点插入到左子树中，否则：
- 把s所指节点插入到右子树中。**（新插入节点总是叶子节点）**

```cpp
int bstree_insert(bstree_t *tree, bstree_t *k)
{
    if (NULL == tree) {
        tree = k;           /* k的内存由外部分配 */
        k->left = NULL;
        k->right = NULL;    /* 新插入的节点总是叶子节点，故左右子树都为NULL */

        return true;

    /* 相等表示有重复，报错 */
    } else if (tree->key == k->key) {
        return false;

    } else if (k->key < tree->key) {
        bstree_insert(tree->left, k);

    } else {
        bstree_insert(tree->right, k);
    }
}
```