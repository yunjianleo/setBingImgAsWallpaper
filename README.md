# setBingImgAsWallpaper
将必应每日图设为桌面壁纸

```javascript
import web.rest.jsonClient;
import inet.http;
import gdip;
import fsys;
var bing_url = "https://bing.com/HPImageArchive.aspx?format=js&idx=0&n=1"


//获取网页图片
var client = web.rest.jsonClient();
var http = inet.http();
result = client.get(bing_url)
img_url = result[["images"]][1]["url"];
full_img_url = "https://bing.com" + img_url;
var img_content = http.get(full_img_url)

//保存为位图
var wallpaper_path = fsys.getTempDir() ++ "wallpaper.bmp"
var img_object = gdip.image(img_content) //加载原图片，创建图片对象
img_object.save(wallpaper_path)
img_object.dispose() //释放图片对象


//设置壁纸显示方式(平铺、居中、拉伸)
import win.reg;
regedit = win.reg("HKEY_CURRENT_USER\Control Panel\Desktop")
regedit.setSzValue("TileWallpaper","0") //好像是拉伸，不确认
regedit.close();

//调用API函数修改壁纸
SystemParametersInfo = ::User32.api("SystemParametersInfoA","int(int uAction,int uParam,string lpvParam,int fuWinIni)")
//用API函数修改壁纸
SystemParametersInfo(0x14/*_SPI_SETDESKWALLPAPER*/,0, wallpaper_path, 1 | 2 /*_SPIF_UPDATEINIFILE | _SPIF_UPDATEINIFILE*/);
http.close();
client.close();
```
