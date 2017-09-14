## RxJava系列-from类操作符（二）
通常将一组数据（数组和集合）发射出去，使用create方法如下：

```
        final String[] strs={"Hello World!","我是超人！"};
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> e) throws Exception {
                for (String str:strs){
                    e.onNext(str);
                }

            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                System.out.println("收到消息:"+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```
可以简化为使用from...类的操作符

* 数组类的：

```
        String[] strs={"Hello World!","我是超人！"};
        Observable.fromArray(strs).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                System.out.println("接收到消息："+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```
* 集合类：

```
        List<String> strs=new ArrayList<>();
        strs.add("Hello World!");
        strs.add("我是超人!");
        Observable.fromIterable(strs).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onNext(@NonNull String s) {
                System.out.println("接收到消息："+s);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });
```