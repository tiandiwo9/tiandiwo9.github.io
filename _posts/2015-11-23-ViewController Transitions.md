---
layout: post
title: View Controller Transitions
date: 2015-11-23 15:32:24.000000000 +09:00
tags: [Transitions , AwesomeUI]
---

## 一、简介
在 iOS 7 之前，我们只能使用系统提供的转场效果，几种方法如下：
>     public static var TransitionNone: UIViewAnimationOptions { get } // default
>     public static var TransitionFlipFromLeft: UIViewAnimationOptions { get }
>     public static var TransitionFlipFromRight: UIViewAnimationOptions { get }
>     public static var TransitionCurlUp: UIViewAnimationOptions { get }
>     public static var TransitionCurlDown: UIViewAnimationOptions { get }
>     public static var TransitionCrossDissolve: UIViewAnimationOptions { get }
>     public static var TransitionFlipFromTop: UIViewAnimationOptions { get }
>     public static var TransitionFlipFromBottom: UIViewAnimationOptions { get }

大部分时候够用，但仅仅是够用而已，你想让这个转场动画有交互式的效果就更难了。

iOS 7 开放了相关 API 允许我们对转场效果进行全面定制，这太棒了，自定义转场动画以及对交互手段的支持带来了无限可能。

## 二、Transitions详解
下图是从 WWDC 2013 Session 218 整理的，解释了转场时视图控制器和其对应的视图在结构上的变化：

![结构上的变化](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/The%20Anatomy%20of%20Transition.png)

转场过程中，作为容器的父 VC 维护着多个子 VC，但在视图结构上，只保留一个子 VC 的视图，所以转场的本质是下一场景(子 VC)的视图替换当前场景(子 VC)的视图以及相应的控制器(子 VC)的替换，表现为当前视图消失和下一视图出现，基于此进行动画，动画的方式非常多，所以限制最终呈现的效果就只有你的想象力了。Parent VC 可为 UIViewController, UITabbarController 或 UINavigationController 中的任何一种。

官方支持以下几种方式的自定义转场：

1. 在 UINavigationController 中 push 和 pop;
1. 在 UITabBarController 中切换 Tab;
1. Modal 转场：present 和 dismiss;
1. UICollectionViewController 的布局转场：UICollectionViewController 与 UINavigationController 结合的转场方式。

iOS 7 以协议的方式开放了自定义转场的 API。转场协议由5种协议组成，在实际中只需要我们提供其中的两个或三个便能实现绝大部分的转场动画：

#### 1.Transition Delegate（转场代理）
根据不同的转场类型方便的提供需要的动画控制器和交互控制器。
> UINavigationControllerDelegate    //UINavigationController 的 delegate 属性遵守该协议。
> UITabBarControllerDelegate  //UITabBarController 的 delegate 属性遵守该协议。
> UIViewControllerTransitioningDelegate  //UIViewController 的 transitioningDelegate 属性遵守该协议。

UIViewControllerTransitioningDelegate 是 iOS 7 新增的协议。

#### 2.Transition Context（转场环境）
定义了转场时需要的元数据，比如在转场过程中所参与的视图控制器和视图的相关属性。 转场上下文对象遵从 UIViewControllerContextTransitioning 协议，并且这是由系统负责生成和提供的。
> public protocol UIViewControllerContextTransitioning : NSObjectProtocol

#### 3.Animation Controllers（动画控制器）
遵从 UIViewControllerAnimatedTransitioning 协议，并且负责实际执行动画。

![Animation Controllers](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/IMG_0044.PNG)

> public protocol UIViewControllerAnimatedTransitioning : NSObjectProtocol

    // This is used for percent driven interactive transitions, as well as for container controllers that have companion animations that might need to
    // synchronize with the main animation. 
    public func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval
    // This method can only  be a nop if the transition is interactive and not a percentDriven interactive transition.
    public func animateTransition(transitionContext: UIViewControllerContextTransitioning)
    
#### 4.Interaction Controllers（交互控制器）
通过遵从 UIViewControllerInteractiveTransitioning 协议来控制可交互式的转场。

![Interaction Controllers](https://raw.githubusercontent.com/GarfieldLover/GarfieldLover.github.io/master/assets/postImages/IMG_0046.PNG)

> public protocol UIViewControllerInteractiveTransitioning : NSObjectProtocol 

```
   public func startInteractiveTransition(transitionContext: UIViewControllerContextTransitioning)
```

#### 5.Transition Coordinators（转场协调器） 
可以在运行转场动画时，并行的运行其他动画。主要在 Modal 转场和交互转场取消时使用，其他时候很少用到。 转场协调器遵从 UIViewControllerTransitionCoordinator 协议。

## 三、非交互转场与交互转场实现
动画控制器遵守UIViewControllerAnimatedTransitioning协议，该协议要求实现以下方法：

    // 返回动画时间。
    public func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval
    //执行动画的地方，最核心的方法。
    public func animateTransition(transitionContext: UIViewControllerContextTransitioning)

在转场开始前生成遵守转场环境协议UIViewControllerContextTransitionin的对象 transitionContext，它有以下几个方法来提供动画控制器需要的信息：
 
    //返回容器视图，转场动画发生的地方。
    public func containerView() -> UIView?
    //获取参与转场的视图控制器，有 UITransitionContextFromViewControllerKey 和 UITransitionContextToViewControllerKey 两个 Key。 
    public func viewControllerForKey(key: String) -> UIViewController?
    //获取参与参与转场的视图，有 UITransitionContextFromViewKey 和 UITransitionContextToViewKey 两个 Key。
    public func viewForKey(key: String) -> UIView?

前面提到转场的本质是下一个场景的视图替换当前场景的视图，从当前场景过渡下一个场景。下面称即将消失的场景的视图为 fromView，对应的视图控制器为 fromVC，即将出现的视图为 toView，对应的视图控制器称之为 toVC。几种转场方式的转场操作都是可逆的，一种操作里的 fromView 和 toView 在逆向操作里的角色互换成对方，fromVC 和 toVC 也是如此。在动画控制器里，参与转场的视图只有 fromView 和 toView 之分，与转场方式无关。转场动画的最终效果只限制于你的想象力。

#### TabBarController 滑动切换
首先设置Transition Delegate，返回UIViewControllerAnimatedTransitioning 和 UIViewControllerInteractiveTransitioning实现类。

    func tabBarController(tabBarController: UITabBarController, animationControllerForTransitionFromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning?{
        let fromIndex = tabBarController.viewControllers!.indexOf(fromVC)!
        let toIndex = tabBarController.viewControllers!.indexOf(toVC)!
        
        let tabChangeDirection: TabOperationDirection = toIndex < fromIndex ? .Left : .Right
        let transitionType = SDETransitionType.TabTransition(tabChangeDirection)
        let slideAnimationController = SlideAnimationController(type: transitionType)
        return slideAnimationController
    }
    
    func tabBarController(tabBarController: UITabBarController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        return interactive ? interactionController : nil
    }
    
动画控制器的核心代码

    func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval {
        return 0.3
    }
    
    func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    //容器
        guard let containerView = transitionContext.containerView(),
        //当前vc
            fromVC = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey),      
            //转向vc
            toVC = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)
            else{
            return  
        }

        
        let fromView = fromVC.view
        let toView = toVC.view
        
        var translation = containerView.frame.width
        var toViewTransform = CGAffineTransformIdentity
        var fromViewTransform = CGAffineTransformIdentity
        
        switch transitionType{
        case .NavigationTransition(let operation):
            translation = operation == .Push ? translation : -translation
            toViewTransform = CGAffineTransformMakeTranslation(translation, 0)
            fromViewTransform = CGAffineTransformMakeTranslation(-translation, 0)
        case .TabTransition(let direction):
        //从Transition Delegate 返回了 是向右还是向左
            translation = direction == .Left ? translation : -translation
            fromViewTransform = CGAffineTransformMakeTranslation(translation, 0)
            toViewTransform = CGAffineTransformMakeTranslation(-translation, 0)
        case .ModalTransition(let operation):
            translation =  containerView.frame.height
            toViewTransform = CGAffineTransformMakeTranslation(0, (operation == .Presentation ? translation : 0))
            fromViewTransform = CGAffineTransformMakeTranslation(0, (operation == .Presentation ? 0 : translation))
        }

        switch transitionType{
        case .ModalTransition(let operation):
            switch operation{
            case .Presentation: containerView.addSubview(toView)
                //在 dismissal 转场中，不要添加 toView，否则黑屏
            case .Dismissal: break
            }
        default: containerView.addSubview(toView)
        }
        
        toView.transform = toViewTransform
        //开始动画
        UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
            //toView不变，fromView移走？？？
            fromView.transform = fromViewTransform
            toView.transform = CGAffineTransformIdentity
            }, completion: { finished in
                fromView.transform = CGAffineTransformIdentity
                toView.transform = CGAffineTransformIdentity
                
                let isCancelled = transitionContext.transitionWasCancelled()
                transitionContext.completeTransition(!isCancelled)
        })
    }
    
实现交互化，在非交互转场的基础上将之交互化需要两个条件：

1. 由转场代理提供交互控制器，这是一个遵守`UIViewControllerInteractiveTransitioning`协议的对象，不过系统已经打包好了现成的类UIPercentDrivenInteractiveTransition供我们使用。我们不需要做任何配置，仅仅在转场代理的相应方法中提供一个该类实例便能工作。另外交互控制器必须有动画控制器才能工作。
1. 交互控制器还需要交互手段的配合，最常见的是使用手势，或是其他事件，来驱动整个转场进程。



    func handlePan(panGesture: UIPanGestureRecognizer){
        let translationX =  panGesture.translationInView(view).x
        let translationAbs = translationX > 0 ? translationX : -translationX
        let progress = translationAbs / view.frame.width
        switch panGesture.state{
        case .Began:
                //更新交互状态
            tabBarVCDelegate.interactive = true
            let velocityX = panGesture.velocityInView(view).x
            if velocityX < 0{
                if selectedIndex < subViewControllerCount - 1{
                    selectedIndex += 1
                }
            }else {
                if selectedIndex > 0{
                    selectedIndex -= 1
                }
            }
                //2.更新进度：
        case .Changed:
            tabBarVCDelegate.interactionController.updateInteractiveTransition(progress)
        case .Cancelled, .Ended:
            if progress > 0.3{
                        //完成转场。
                tabBarVCDelegate.interactionController.completionSpeed = 0.99
                tabBarVCDelegate.interactionController.finishInteractiveTransition()
            }else{
            //或者，取消转场。
                tabBarVCDelegate.interactionController.completionSpeed = 0.99
                tabBarVCDelegate.interactionController.cancelInteractiveTransition()
            }
                    //无论转场的结果如何，恢复为非交互状态。
            tabBarVCDelegate.interactive = false
        default: break
        }
    }

[-------------------demo地址------------------ ](https://github.com/GarfieldLover/iOS-ViewController-Transition-Demo)

## 四、UICollectionViewController 布局转场（非常好玩）
前面一直没有提到这种转场方式，与三大主流转场不同，布局转场只针对 CollectionViewController 搭配 NavigationController 的组合，且是作用于布局，而非视图。采用这种布局转场时，NavigationController 将会用布局变化的动画来替代 push 和 pop 的默认动画。苹果自家的照片应用中的「照片」Tab 页面使用了这个技术：在「年度-精选-时刻」几个时间模式间切换时，CollectionViewController 在 push 或 pop 时尽力维持在同一个元素的位置同时进行布局转换。

    //根据点击 cell 的位置来决定下一级的 CollectionView 的布局
    override func collectionView(collectionView: UICollectionView, didSelectItemAtIndexPath indexPath: NSIndexPath) {
    //改变layout 可以创造出不同布局
        let layout = UICollectionViewFlowLayout()
        layout.itemSize = CGSize(width: indexPath.item * 10, height: indexPath.item * 10)
        layout.sectionInset = UIEdgeInsets(top: 10, left: 10, bottom: 0, right: 10)
        //随机更改 cell 的数量
        dataSource.cellCount = Int(UInt32(arc4random()) % UInt32(10)) + 5
        let nextCVC = CollectionVCTestTwo(collectionViewLayout: layout)
        nextCVC.useLayoutToLayoutNavigationTransitions = true
        navigationController?.pushViewController(nextCVC, animated: true)
    }
    
## 五、Awesome开源代码分析
转场动画里转场部分的实现其实很简单，大部分复杂的转场动画与本文范例里简单的转场动画相比，复杂的部分在动画部分，转场的部分都是一样的。


类库 | 简介
---|---
VCTransitionsLibrary | 该库提供了多达10种转场效果，从技术上讲，大部分效果都是针对 transform 进行动画，如果你对这些感兴趣或是恰好有这方面的使用需求，可以学习这些效果的实现，从代码角度看，封装技巧也很值得学习，这个库是学习转场动画的极佳范例
StarWars | 这个转场动画在视觉上极其惊艳，一出场便获得上千星星的青睐，它有贴合星战内涵的创意设计和惊艳的视觉表现，以及优秀的性能优化
BubbleTransition | Mask 动画往往在视觉上令人印象深刻，这种动画通过使用一种特定形状的图形作为 mask 截取当前视图内容，使得当前视图只表现出 mask 图形部分的内容
RadialTransition_swift | mask 2

### [VCTransitionsLibrary](https://github.com/ColinEberhardt/VCTransitionsLibrary)
#### 1.代码封装

TRViewControllerAnimatedTransitioning实现UIViewControllerAnimatedTransitioning协议，取得当前转场类型TransitionStatus、转场环境transitionContext等。
```
public protocol TRViewControllerAnimatedTransitioning: UIViewControllerAnimatedTransitioning {
```
封装TransitionInteractive协议。

    var percentTransition: UIPercentDrivenInteractiveTransition?{get set}



下列分别是present、push和tabbar的TransitioningDelegate实现。

    //返回present transition实例
    public func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        transition.transitionStatus = .Present
        return transition
    }
    //返回Dismiss transition实例
    public func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        transition.transitionStatus = .Dismiss
        return transition
    }
     //返回InteractiveTransitioning实例，也就是UIPercentDrivenInteractiveTransition
    public func interactionControllerForDismissal(animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        guard let transition = transition as? TransitionInteractiveable else {
            return nil
        }
        return transition.interacting ? transition.percentTransition : nil
    }
    
    public func interactionControllerForPresentation(animator: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        guard let transition = transition as? TransitionInteractiveable else {
            return nil
        }
        return transition.interacting ? transition.percentTransition : nil
    }
    
UIViewController+TRPresent这个类别中封装了presentViewController和dismissViewControllerAnimated方法。
主要实现了

    //设置vc的transitioningDelegate
    viewControllerToPresent.transitioningDelegate = transitionDelegate

在PushTransition中加入了UIPanGestureRecognizer，在手势滑动的各状态下更新InteractiveTransition的状态。

       switch recognizer.state {
        case .Began :
            transition.interacting = true
            transition.percentTransition = UIPercentDrivenInteractiveTransition()
            //start
            transition.percentTransition?.startInteractiveTransition((transition as! TRViewControllerAnimatedTransitioning).transitionContext!)
            toVC!.navigationController!.tr_popViewController()
        case .Changed :
        //更新进度
            transition.percentTransition?.updateInteractiveTransition(percent)
        default :
            transition.interacting = false
            if percent > transition.interactivePrecent {
                transition.cancelPop = false
                transition.percentTransition?.completionSpeed = 1.0 - transition.percentTransition!.percentComplete
                //完成并移除
                transition.percentTransition?.finishInteractiveTransition()
                fromVC?.view.removeGestureRecognizer(edgePanGestureRecognizer)
            } else {
            //end状态，如果进度大于0.3就完成，没有就取消
                transition.cancelPop = true
                transition.percentTransition?.cancelInteractiveTransition()
            }
            transition.percentTransition = nil
        }
        
TabBarTransition实现返回

    public func tabBarController(tabBarController: UITabBarController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
        guard let transitionAnimation = transitionAnimation as? TabBarTransitionInteractiveable else {
            return nil
        }
        return transitionAnimation.interacting ? transitionAnimation.percentTransition : nil
    }
    
    public func tabBarController(tabBarController: UITabBarController, animationControllerForTransitionFromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return transitionAnimation
    }
    //设置动画
     tabBarController.tr_transitionDelegate = TRTabBarTransitionDelegate(method: TRTabBarTransitionMethod.Slide)
   
    //点击tab时调用动画
    public func tr_selected(index: ViewControllerIndex, gesture: UIGestureRecognizer, completion: (() -> Void)? = nil) 

动画生成工厂方法模式类       


```
pushTransition.append(PushTransition(name: "OmniFocus", imageName: "OmniFocus60x60", pushMethod: .OMNI(keyView: logoImageView), interactive: false))
    
    struct PresentTransition {
	let name: String
	let imageName: String
	let presentMethod: TRPresentTransitionMethod
	let interactive: Bool
    }
```
 TRPresentTransitionMethod 根据type创建相对的TransitionAnimation类型。  push的时候传入 method: updateTransition，最终给了navigationController的delegate方法。                                                                     		
```
navigationController?.tr_pushViewController(vc, method: updateTransition, statusBarStyle: .LightContent, completion: {
```
在转场过程中就会调用具体动画类里的方法。

    public func animateTransition(transitionContext: UIViewControllerContextTransitioning) {

#### 2.具体转场动画核心代码


分类 | 核心代码
---|---
OMNI | fromVC!.view.layer.mask截取上半部分，bottomView截图下半部分，containView?.addSubview， fromVC!.view.layer.position.y -= topHeight， self.bottomView.layer.position.y += bottomHeight
IBanTang | containView?.layer.addSublayer(lightMaskLayer),添加遮罩，keyViewCopy.layer.position 变化
Fade | toVC!.view.layer.opacity = 0 , toVC!.view.layer.opacity = 1
Page |  fromVC?.view.layer.transform = transform3D   , toVC?.view.layer.position.x = endPositionX + toVC!.view.layer.bounds.width / 2
Blixt | self.keyViewCopy.frame变化，                fromVC?.view.layer.frame.origin.x = -rightX，  toVC?.view.layer.frame.origin.x = leftX
Twitter |CATransform3DRotate 角度变化  fromVC?.view.layer.transform = transform,  toVC?.view.layer.frame = finalFrame
PopTip |  maskView.frame = screenBounds,  maskView.alpha = startOpacity,  toVC?.view.layer.frame = startFrame ,            toVC?.view.layer.frame = finalFrame
TaaskyFlip | startTransform = CATransform3DRotate(startTransform, CGFloat(angle), 0, 1, 0)  , toVC?.view.layer.transform = endTransform
Elevate | maskLayerAnimation.fromValue = NSValue(CGSize: startSize), maskLayerAnimation.toValue = NSValue(CGSize: endSize),        maskViewPositionAnimation.fromValue = NSValue(CGPoint: startPosition),  maskViewPositionAnimation.toValue = NSValue(CGPoint: endPosition)
Scanbot |  toVC?.view.frame.origin.y = toVCEndY, fromVC?.view.frame.origin.y = fromVCEndY , var percent = offsetY / view.bounds.size.height,           percentTransition?.updateInteractiveTransition(percent)

未完待续～～～
