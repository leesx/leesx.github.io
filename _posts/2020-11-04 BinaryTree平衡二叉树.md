### 二叉树 BinaryTree

平衡二叉树  

- 前序遍历  `先找自己，再找左子节点，再找右子节点`
- 中序遍历  `先找左子节点，再找右子节点`
- 后序遍历  `先找左子节点，再找右子节点，需要找完自己的左右子节点`
- 找最小值  `只需要查找左子节点`
- 找最大值  `只需要查找右子节点`

```
function BinaryTree() {
        var Node = function (key) {
          this.key = key;

          this.left = null;

          this.right = null;
        };

        this.root = null;

        var insertNode = function (node, newNode) {
          if (newNode.key < node.key) {
            if (node.left === null) {
              node.left = newNode;
            } else {
              insertNode(node.left, newNode);
            }
          } else if (newNode.key > node.key) {
            if (node.right === null) {
              node.right = newNode;
            } else {
              insertNode(node.right, newNode);
            }
          }
        };

        this.insert = function (key) {
          if (this.root === null) {
            this.root = new Node(key);
          } else {
            insertNode(this.root, new Node(key));
          }
        };

        //栈是先进后出的，所以在节点1的时候，它没有左子节点，这个时候开始出栈，继续执行上一次的inOrderTraverceNodes里未执行完的代码，当节点1也没有右子节点的时候，到节点3开始继续执行上一次的inOrderTraverceNodes里未执行完的代码，以此类推。

        //栈是先进后出的，一开始节点8执行函数，有左子节点3，节点3执行函数，有左子节点1，节点1执行函数，没有左子节点，这时是null，这时这个函数已经出栈，到节点1之前执行的函数开始出栈了，然后调用callback，打印了1，然后节点1传入node.right,这时又是null，然后这个函数出栈了，开始到节点3之前执行的函数出栈了，所以进栈的顺序是8,3,1,1的左null,出栈的顺序是1的左null，1，1的右null，3，3执行后，右边有个6，6进栈，6有4，4进栈，这个时候栈里还有8,3,,6,4

        //中序遍历代码,先找左子节点，再找右子节点

        var inOrderTraverceNodes = function (node, callback) {
          if (node !== null) {
            inOrderTraverceNodes(node.left, callback);

            callback(node.key);
            //调动callback

            inOrderTraverceNodes(node.right, callback);
          }
        };

        this.inOrderTraverce = function (callback) {
          inOrderTraverceNodes(this.root, callback);
        };

        //前序遍历代码,先找自己，再找左子节点，再找右子节点

        var preOrderTraverceNodes = function (node, callback) {
          if (node !== null) {
            callback(node.key);
            //调动callback

            preOrderTraverceNodes(node.left, callback);

            preOrderTraverceNodes(node.right, callback);
          }
        };

        this.preOrderTraverce = function (callback) {
          preOrderTraverceNodes(this.root, callback);
        };

        //后序遍历代码,先找左子节点，再找右子节点，需要找完自己的左右子节点

        var postOrderTraverceNodes = function (node, callback) {
          if (node !== null) {
            postOrderTraverceNodes(node.left, callback);

            postOrderTraverceNodes(node.right, callback);

            callback(node.key);
            //调动callback
          }
        };

        this.postOrderTraverce = function (callback) {
          preOrderTraverceNodes(this.root, callback);
        };

        //查找最小值，只需要查找左子节点

        var minNode = function (node) {
          if (node) {
            while (node && node.left !== null) {
              node = node.left;
            }

            return node.key;
          }

          return null;
        };

        this.min = function () {
          return minNode(this.root);
        };

        //查找最大值，只需要查找右子节点

        var maxNode = function (node) {
          if (node) {
            while (node && node.right !== null) {
              node = node.right;
            }

            return node.key;
          }

          return null;
        };

        this.max = function () {
          return minNode(this.root);
        };

        //查找值x

        var searchNode = function (node, key) {
          if (node === null) {
            return false;
          }

          if (key < node.key) {
            return searchNode(node.left, key);
          } else if (key > node.key) {
            return searchNode(node.right, key);
          } else {
            return true;
          }
        };

        this.search = function (key) {
          return searchNode(this.root, key);
        };

        //查找最小值，只需要查找左子节点

        var findMinNode = function (node) {
          if (node) {
            while (node && node.left !== null) {
              node = node.left;
            }

            return node;
          }

          return null;
        };

        //删除叶子节点

        var removeNode = function (node, key) {
          if (node === null) {
            return null;
          }

          if (key < node.key) {
            node.left = removeNode(node.left, key);

            return node;
          } else if (key > node.key) {
            node.right = removeNode(node.right, key);

            return node;
          } else {
            if (node.left === null && node.right === null) {
              node = null;

              return node;
            }

            if (node.left === null) {
              //把右子节点覆盖当前节点

              node = node.right;

              return node;
            } else if (node.right === null) {
              //把右子节点覆盖当前节点

              node = node.left;

              return node;
            } else {
              //例如删除的是节点3，走到这里当前节点就是节点3了，传入节点6，找到它的最小节点，返回当前节点，修改节点3的key为节点4，传入节点6和要删除的节点4，删除后重新对节点6赋值，在返回其父节点4

              var aux = findMinNode(node.right);

              node.key = aux.key;

              node.right = removeNode(node.right, aux.key);

              return node;
            }
          }
        };

        this.remove = function (key) {
          removeNode(this.root, key);
        };
      }

      var nodes = [8, 3, 10, 1, 6, 14, 4, 7, 13];

      var binaryTree = new BinaryTree();

      nodes.forEach((key) => {
        binaryTree.insert(key);
      });

      console.dir(binaryTree.root);

      var callback = function (key) {
        console.log(key);
      };

      binaryTree.preOrderTraverce(callback);

      // console.log('this is min: ' + binaryTree.min());

      // console.log('this is search: ' + binaryTree.search(7));

      // console.log('this is search: ' + binaryTree.search(9));

      // binaryTree.remove(1);
```
