#百度地图定位Android版插件
本插件利用百度地图提供的定位功能进行Android版手机定位。
  
####为什么没有iOS版？
因为iOS版有官方的定位插件_cordova-plugin-geolocation_可以使用。
补充：使用百度经纬应该还是能精确些的，后续加上吧

请参照：[cordova-plugin-geolocation](https://github.com/apache/cordova-plugin-geolocation)

####Android版为什么不使用官方的_cordova-plugin-geolocation_插件
最新版的插件已经删除掉的Android版定位的代码，改为基于系统浏览器(chrome内核)进行定位。

为什么这样做，也有人问过同样的问题，作者的回答是这样比原生定位更快更准确。

但经过测试后，发现根本无法定位，几经调查发现跟貌似国内网络有关系，原因相信大家都懂的，此过省略好几个字。。。。
补充：在实际开发过程中，大部分手机能定位，但也存在无法经纬的情况，所以转到了百度定位。经纬经度也不需要额外调用其他接口了。

__此插件就这么诞生了__

####版本
基于百度地图Android版定位SDK（v7.1）

####一，申请密钥
请参照：[申请密钥Android定位SDK](http://developer.baidu.com/map/index.php?title=android-locsdk/guide/key)

####二，安装插件

```
ionic plugin add https://github.com/chenxinbin/cordova-qdc-baidu-location --variable API_KEY="<API_KEY>"
**注：此处的API_KEY来自于第一步，不带左右尖括号**
```

####三，使用方法

```
// 进行定位
baidu_location.getCurrentPosition(successCallback, failedCallback);
```

获得定位信息，返回JSON格式数据:

```
{
  latitude : 纬度,
  longitude: 经度,(补充：原git中为lontitude，相信是拼写错误)
  ...
}
```
具体字段内容请参照：[BDLocation](http://developer.baidu.com/map/loc_refer/index.html)

####四，查看当前安装了哪些插件

```
ionic plugin ls
```

####五，删除插件

```
ionic plugin rm com.qdc.plugins.baidu.location
```
####六. Ionic 2 实例
```
declare let baidu_location; //import后马上声明

  getCurrentPosition()
  {
      //console.log("android");
      if(this.platform.is("android"))
          return new Promise(
          function(resolve, reject) {
              baidu_location.getCurrentPosition(
                function(data){
                    var coords = JSON.parse(data);
                    if(coords["status"]=='SUCCESS')
                    {
                        resolve(coords);
                    }else{
                        reject(coords["describe"]);
                    }
                }, 
                function(err){
                  reject(err);
                }
              );
          });
      else
          return new Promise(
              function(resolve, reject) {
                 this.geolocation.getCurrentPosition({timeout: 10000}).then((resp) => {
                    resolve(resp.coords);
                  }).catch((error) => {
                      let msg = '定位失败！';
                      if(error.code == error.PERMISSION_DENIED)
                          msg += "请确认本软件拥有定位权限";
                      else if(error.code == error.POSITION_UNAVAILABLE)
                          msg += "无法定位，请确认联网或打开定位功能";
                      else if(error.code == error.TIMEOUT)
                          msg += "定位超时，请重试...";
                      reject(msg);
                  });
              });
  }
```







