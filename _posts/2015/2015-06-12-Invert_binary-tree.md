---
layout: post
title: 二叉树的反转有多难
categories:
- 数据结构与算法
tags:
- 数据结构与算法
---

###起因
今天看到个很有意思的讨论，说的是Homebrew的作者Max Howell去Google面试，然后失败了，
失败的原因竟然是面试的HR根本没问任何IOS开发的问题（他可以说是ios开发的专家了，开发过几款比较有名的app），
而是他没能在白板上写出反转二叉树的程序。其实仔细想想用递归的话还真不是什么难题, 因为很容易写啊。  

马上动手实现：

```ruby
# 首先我们建个二叉树的结构出来，二叉树包含的元素很简单：
# 1. 一个value的值
# 2. 最多左右两个子节点
module BinaryTree

  class Node
    attr_accessor :value, :left, :right
    include Enumerable

    def initialize(value)
      @value = value
    end

  end

  def self.generate_data
    # 准备二叉树数据
    data = {
      :value => 6,
      :left => {
        :value => 3,
        :left => {
          :value => 1
        },
        :right => {
          :value => 6
        }
      },
      :right => {
        :value => 8,
        :left => {
          :value => 5
        },
        :right => {
          :value => 9
        }
      }           
    }
  end

# 写个方法填充二叉树并调用
  def self.fill_tree data, node
    if data[:left]
      node.left = Node.new(data[:left][:value])
      fill_tree data[:left], node.left
    end
    if data[:right]
      node.right = Node.new(data[:right][:value]) 
      fill_tree data[:right], node.right
    end
  end

# 开始写今天的主题了，反转二叉树。
# 如果我没理解错的话，所谓的反转应该就是左右叶子节点交换
# 左右互换，递归就行，

  def invert node
    tmp = node.left
    node.left = node.right if node.right
    node.right = tmp if tmp

    invert node.left if node.left
    invert node.right if node.right
  end


end

# 测试一下吧，写几个简单的测试用例
require 'test/unit'

class TestInvert < Test::Unit::TestCase

  def test
    prepare

    assert_equal(@original_tree.left.value, 3)
    assert_equal(@original_tree.right.value, 8)
    assert_equal(@original_tree.left.left.value, 1)
    assert_equal(@original_tree.left.right.value, 6)
    assert_equal(@original_tree.right.left.value, 5)
    assert_equal(@original_tree.right.right.value, 9)

    assert_equal(@inverted_tree.left.value, 8)
    assert_equal(@inverted_tree.right.value, 3)
    assert_equal(@inverted_tree.left.left.value, 9)
    assert_equal(@inverted_tree.left.right.value, 5)
    assert_equal(@inverted_tree.right.left.value, 6)
    assert_equal(@inverted_tree.right.right.value, 1)
  end

  def prepare
    data = BinaryTree.generate_data
    @node_root = BinaryTree::Node.new(data[:value])
    BinaryTree.fill_tree data, @node_root

    @original_tree = Marshal.load(Marshal.dump(@node_root))

    BinaryTree.invert @node_root
    @inverted_tree = @node_root
  end

end

#最后跑一下测试用例，0个error,搞定了！
Finished tests in 0.983710s, 1.0166 tests/s, 12.1987 assertions/s.
1 tests, 12 assertions, 0 failures, 0 errors, 0 skips

```

###总结

其实用递归来解这个问题是很容易的，迭代的话少许有些难度，毕竟递归才是最符合人类思维方式的，虽然效率一般不高。  
对于Google这种的公司使用一些基础的算法题来过滤面试者也无可厚非，但是那个HR可能没对面试者进行详细的了解，也可能他自己也没想到一个大牛会栽在一道简单的算法题上。

