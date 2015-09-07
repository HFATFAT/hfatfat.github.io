---
layout:     post
title:      Android Handler
date:       2015-08-24 14:12:02
author:     HFF  
summary:    Android Handler
categories: jekyll
thumbnail:  heart
tags:
 - Android
---

#####Handler负责发送消息，Looper负责接收Handler发送的消息，并直接把消息回传给Handler自己，MessageQueue是一个存储消息的容器。

###用法一：
<pre class="brush:java;gutter: true;">
private Handler handler=new Handler();
.......
 handler.post(new Runnable() {
            @Override
            public void run() {
                textView.setText("Update");
            }
        }); 
</pre>


###用法二：
<pre class="brush:java;gutter: true;">
private Handler handler=new Handler(){
        public void handleMessage(Message msg){
          textView.setText(""+msg.arg1);
        }
    };
........
Message message=handler.obtainMessage(); //Message message=new Message();
message.arg1=1;
handler.sendMessage(message);  //message.sendToTarget()
</pre>


###用法三：
<pre class="brush:java;gutter: true;">
private Handler handler = new Handler()
private MyRunnable myRunnable=new MyRunnable();
class MyRunnable implements Runnable{
   @Override
    public void run() {
       handler.postDelayed(myRunnable,1000);
    }
}
........
handler.postDelayed(myRunnable,1000);

handler.removeCallbacks(myRunnable); //移除消息
</pre>


###用法四：
<pre class="brush:java;gutter: true;">
 private Handler handler = new Handler(new Handler.Callback() {
        @Override
        public boolean handleMessage(Message msg) {
            return false; // return true 消息被截获
        }
    }){
    @Override
    public void handleMessage(Message msg) {
    }
    };
</pre>

#####更新...