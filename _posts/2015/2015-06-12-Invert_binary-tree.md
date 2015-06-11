---
layout: post
title: 反转二叉树
categories:
- 数据结构与算法
tags:
- 数据结构与算法
---

##二叉树的反转有多难
今天看到个很有意思的讨论，说的是Homebrew的作者去Google面试，然后失败了，
失败的原因竟然是面试的HR根本没问任何IOS开发的问题（他可以说是ios开发的专家了，开发过几款比较有名的app）
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
    node.left = node.right
    node.right = tmp

    invert node.left if node.left
    invert node.right if node.right
  end


end

# 测试一下吧，写几个简单的测试用例
require 'test/unit'

class TestInvert < Test::Unit::TestCase

  def test
    prepare
    assert_equal(@node_root.left.value, 8)
    assert_equal(@node_root.right.value, 3)
  end

  def prepare
    data = BinaryTree.generate_data
    @node_root = BinaryTree::Node.new(data[:value])
    BinaryTree.fill_tree data, @node_root

    @original_tree = @node_root
    BinaryTree.invert @node_root
    @inverted_tree = @node_root
  end

end


```

