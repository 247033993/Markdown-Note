## RxJava系列-just操作符（一）
正常情况下发送一个数据,使用create的写法如下：

```
       Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> e) throws Exception {
                //发射一个字符串
                e.onNext("Hello World!");
            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                //接收到一个字符串
                System.out.println("接收到数据："+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```
我们可以使用just操作符来简化这种写法，如：

```
        Observable.just("Hello World!").subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                System.out.println("接收到数据："+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```
使用just操作符传入一个数据，实际上就是简化create...onNext。just操作符也可以发送多个数据，发送多个数据就传入多个参数（just方法入参为可变参数），但是多个参数的类型必须一致，如：

```
        Observable.just("Hello World!","我是超人！").subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                System.out.println("接收到数据："+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
     }
```
上面这样会执行两次onNext方法