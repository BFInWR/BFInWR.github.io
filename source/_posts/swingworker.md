---
title: 在桌面应用开发项目中的异步操作 - SwingWorker
date: 2018-09-05 10:52:00
categories: "Java"
tags:
	- Java
	- SwingWorker
	- 桌面应用程序
	- 异步
	- 项目笔记

---

## 前言
在桌面应用程序开发的过程中难免会去使用数据，那么这些数据来自哪里呢，数据库、服务器、网络、本地文件等到。这些操作我们统一称作费时操作。若是使用同步开发的话，桌面应用程序UI则会等待这些费时操作完成之后才会更新UI，而使用异步的话，则不会影响，我更新我的UI，你获取做完这些费时操作再通知我，把数据传给我就好了。所以在JDK1.6时候推出了——SwingWorker
<!--more -->

```java
javax.swing
Class SwingWorker<T,V>

java.lang.Object
	javax.swing.SwingWorker<T,V>
类型参数：
T- this SwingWorker's doInBackground和getmethods 返回的结果类型
V- 用于通过此方法SwingWorker's publish和process方法执行中间结果的类型
所有已实现的接口：
Runnable，Future <T>，RunnableFuture <T>
```

>  使用Swing编写多线程应用程序时，需要记住两个约束:
>  1. 不应在Event DispatchThread上运行耗时的任务。否则应用程序将无响应。
>  2. 应仅在Event Dispatch Thread上访问Swing组件。
>  这些约束意味着具有时间密集型计算的GUI应用程序需要至少两个线程：1）执行冗长任务的线程和2）所有GUI相关活动的事件调度线程（EDT）。这涉及线程间通信，这可能很难实现。
>  SwingWorker适用于需要在后台线程中运行长时间运行任务并在完成或处理时为UI提供更新的情况。子类SwingWorker必须实现该doInBackground()方法来执行后台计算。

- **工作流程**
- 当前线程：execute()在此线程上调用该方法。它计划SwingWorker在工作 线程上执行 并立即返回。可以等待SwingWorker完成使用get方法。**get() 方法最好在done()方法中调用，这样才能保证操作是完成的 **

- 工作线程：doInBackground() 在此线程上调用该方法。这是所有背景活动应该发生的地方。要通知 PropertyChangeListeners绑定属性更改，请使用 firePropertyChange和 getPropertyChangeSupport()方法。默认情况下，有两个绑定属性可用：state和progress。

- 事件调度线程：此线程上发生所有与Swing相关的活动。SwingWorker调用 process和done()方法并PropertyChangeListeners在此线程上通知任何方法。

**SwingWorker仅设计为执行一次。执行 SwingWorker多次不会导致doInBackground两次调用该 方法。**

**官方实例**
```java
 class PrimeNumbersTask extends
         SwingWorker<List<Integer>, Integer> {
     PrimeNumbersTask(JTextArea textArea, int numbersToFind) {
         //initialize
     }

     @Override
     public List<Integer> doInBackground() {
         while (! enough && ! isCancelled()) {
                 number = nextPrimeNumber();
                 publish(number);
                 setProgress(100 * numbers.size() / numbersToFind);
             }
         }
         return numbers;
     }

     @Override
     protected void process(List<Integer> chunks) {
         for (int number : chunks) {
             textArea.append(number + "\n");
         }
     }
 }

 JTextArea textArea = new JTextArea();
 final JProgressBar progressBar = new JProgressBar(0, 100);
 PrimeNumbersTask task = new PrimeNumbersTask(textArea, N);
 task.addPropertyChangeListener(
     new PropertyChangeListener() {
         public  void propertyChange(PropertyChangeEvent evt) {
             if ("progress".equals(evt.getPropertyName())) {
                 progressBar.setValue((Integer)evt.getNewValue());
             }
         }
     });

 task.execute();
 System.out.println(task.get()); //打印所有的素数
```

**日常使用**
```java
class ProgressWorker extends SwingWorker<List<Data>, Void> {
    private JPanel panel;
    private Boolean isValidateBtn;

    public ProgressWorker(JPanel panel, Boolean isValidateBtn) {
        this.panel = panel;
        this.isValidateBtn = isValidateBtn;
    }

    @Override
    protected void done() {
        panel.setVisible(false);
        try {
            new ShowPage(get(), isValidateBtn); //跳转到下一个界面 并把数据传过去
        } catch (Exception e) {
        }

    }

    @Override
    protected List<Data> doInBackground() {
        return Dispatcher.getDataList(); //重数据库拿到数据
    }

}
```