---
title: "android创建连接热点以及部分手机适配"
featured: /assets/images/pic03.jpg
layout: post
---



之前软件上加入一个换机功能，类似于茄子快传，需要建立热点，连接传输

 1. API简要说明

android中与wifi相关的操作都是在android.net.wifi.WifiManager类中。
    

```
mWifiManager = (WifiManager) context
                .getSystemService(Context.WIFI_SERVICE);
```

 - `int addNetwork (WifiConfiguration
   config)`添加新的Wifi配置并连接，，这个方法返回int，是networkId值。

`

 - int updateNetwork (WifiConfiguration config)`更新配置，如名称密码等

`

 - `WifiInfo getConnectionInfo()`获取当前Wifi连接信息，返回        WifiInfo对象，包括SSID，BSSID，networkId，ipAddress，macAddress等信息

`

 - boolean disableNetwork (int netId)禁用这个network（即使Wifi扫描到这个热点也不会主动去连接），参数netId就是WifiInfo中的networkId值

`

 - boolean disconnect ()` 断开当前连接

`

 - List<WifiConfiguration> getConfiguredNetworks()`返回已经保存的Wifi网络列表，返回值是List<WifiConfiguration>

`




     WifiConfiguration 对象，通过配置 
        SSID: 热点的名字 
        preSharedKey 热点密码 
        hiddenSSID： 是否隐藏SSID
        status：是否启用这个热点配置
        allowedAuthAlgorithms：IEEE 802.11认证算法 OPEN
        allowedGroupCiphers ：组秘钥TKIP+CCMP
        allowedPairwiseCiphers：对称秘钥TKIP+CCMP
        allowedKeyManagement：秘钥管理WPA_PSK
        allowedProtocols：加密协议WPA+RSN
   


 2. 创建热点
 

    了解以上API后，现在开始创建热点。我们进入WifiManager类，发现有创建方法`。
    

    ```
     /**
         * Start AccessPoint mode with the specified
         * configuration. If the radio is already running in
         * AP mode, update the new configuration
         * Note that starting in access point mode disables station
         * mode operation
         * @param wifiConfig SSID, security and channel details as
         *        part of WifiConfiguration
         * @return {@code true} if the operation succeeds, {@code false} otherwise
         *
         * @hide Dont open up yet
         */
        public boolean setWifiApEnabled(WifiConfiguration wifiConfig, boolean enabled) {
            try {
                mService.setWifiApEnabled(wifiConfig, enabled);
                return true;
            } catch (RemoteException e) {
                return false;
            }
        }
    ```
    有了创建热点方法，发现是隐藏的，当然反射拉。方法如下：
    
    ```
     try
        {
            if (isHTCPhone())
            {
                setHTCSSID(cfg);
            }
                
            Method method = mWifiManager.getClass()
                        .getMethod("setWifiApEnabled",
                                WifiConfiguration.class,
                                boolean.class);
            result = (Boolean)method.invoke(mWifiManager,cfg, enable);
            }
            catch (Exception e)
            {
                
                LogUtil.e(TAG, "get an exception " +Log.getStackTraceString(e));
            }
    ```
        如此按照上面，配置好WifiConfiguration ，即可创建热点了。


3 连接热点

     连接wifi也得设置WifiConfiguration ，跟上面设置的一致就好，当然由于android碎片化严重，当然有不少的机器某些协议不支持，导致连接不上的情况。
     

```
 mWifiManager.startScan();//扫描wifi
 
```
通过BroadcastReciver监听`WifiManager.SCAN_RESULTS_AVAILABLE_ACTION`扫描结果，过滤需要连接的wifi

连接wifi热点方法

```
WifiConfiguration wifiConfig=this.setWifiParams(passableHotsPot.get(0));  
int wcgID = wifiManager.addNetwork(wifiConfig);  
boolean flag=wifiManager.enableNetwork(wcgID, true); 
```

4. 适配机型
    首先Htc的手机，细心的朋友发现了吧，在创建热点的时候判断了下htc手机，htc创建热点设置名称密码，代码如下：
    

```
try
                {
                    Field mWifiApProfileField = WifiConfiguration.class.getDeclaredField("mWifiApProfile");
                    mWifiApProfileField.setAccessible(true);
                    Object hotSpotProfile = mWifiApProfileField.get(cfg);
                    mWifiApProfileField.setAccessible(false);
                    
                    //获取SSID
                    Field ssidField = hotSpotProfile.getClass()
                            .getDeclaredField("SSID");
                    ssidField.setAccessible(true);
                    String ssid = (String) ssidField.get(hotSpotProfile);
                    ssidField.setAccessible(false);
                    
                    //获取密码
                    Field keyField = hotSpotProfile.getClass()
                            .getDeclaredField("key");
                    keyField.setAccessible(true);
                    String key = (String) keyField.get(hotSpotProfile);
                    keyField.setAccessible(false);
                    
                    return apCfgSSID.equals(ssid) && apCfgKey.equals(key);
                }
                catch (Exception e)
                {
                    return false;
                }
```
    

另外，在热点连接的时候发现，部分手机连接不上。经定位发现：
扫描配合机的二维码，发现连线失败，将supplicant的配置文件读出来，发现使用了proto=RSN字样，就说明要连接的那个热点只支持WPA2，所以无法连接成功。
因此在创建热点的时候需要在配置WifiConfiguration的时候告知android系统具体使用何种加密方式，在生成热点配置文件时指定proto，连接AP时也指定proto。增加代码如下：

```
WifiConfiguration config = new WifiConfiguration(); 
config.allowedProtocols.clear(); 
config.allowedProtocols.set(WifiConfiguration.Protocol.WPA);
```

当然在热点连接的时候，还需要很多权限。除了wifi控制，网络连接等权限。
定位权限也是必须的。另外还需要在设置中打开位置开关。千万注意。
