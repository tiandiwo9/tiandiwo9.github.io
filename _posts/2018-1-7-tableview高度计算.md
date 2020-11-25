---
layout: post
title: tableview高度计算
date: 2018-1-7 15:32:24.000000000 +09:00
tags: [Swift]
---

UITableViewDynamicLayoutCacheHeight 是一个便捷的，高性能的自动计算使用 Autolayout 布局【Xib、StoryBoard、Masonry 、SnapKit、SDAutoLayout ...】的 UITableViewCell 和 UITableViewHeaderFooterView 的高度，支持横竖屏，内部自动管理高度缓存，已兼容 Swift 。

在 UITableView 获取高度的代理方法里实现如下代码，Block 中的代码和 - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath 中的代码一致即可。

https://github.com/liangdahong/UITableViewDynamicLayoutCacheHeight/blob/master/Images/xib-cell-02.png



框架实现原理
高度计算原理
提前创建 Cell，然后填充内容，然后强制布局，然后获取 Cell 中 MaxY 最大的 View，然后取此 View 的 MaxY 为 Cell 所需高度【所以保证 Cell 中的 View 的 MaxY 最大的值即为 Cell 需要的高度至关重要】，内部会自动管理缓存的保存和清空操作。

如果使用 key 做缓存，表示高度只和 key 有关，只要使用相同的 key 就会得到相同的高度，内部永远不会刷新这个高度「 即使调用了 reloadData 」。


