---
layout: page
title: react项目中前端实现将某页面转化为base64格式图片
parent: 技术文档
---

### react 项目中前端实现将某页面转化为 base64 格式图片

##### 使用 html2canvas 插件将页面 dom 节点转化为图片格式。

1. 安装 html2canvas 插件

   ```yarn命令
   yarn add html2canvas
   ```

2. 在页面中引用

   ```html
   import html2canvas from "html2canvas";
   ```

3. 使用 useRef 钩子函数获取 dom 对象

   ```
   const canvasRef = useRef();
   ```

   在想要转化的最外层 dom 对象添加 ref，如：

   ```
   <div ref={canvasRef} className=...>
   	...
   </div>
   ```

4. 在页面加载时或按钮触发将页面转化

   页面加载时用 useEffect 钩子实现

   ```
   const printDocument = (domElement: any) => {
       if (domEelement) {
         html2canvas(domElement,{
           allowTaint: true,		//解决图片跨域
           scale: 2,		//清晰度，默认为2
           height: element.scrollHeight,	//解决scroll截图不全
           windowHeight: element.scrollHeight,
         }).then((canvas: any) => {
           setImageUrl(canvas.toDataURL("image/jpeg"));	//可转换为其他格式
           console.log("imageUrl", imageUrl);
           // const dlLink = document.createElement("a");    //实现自动下载
           // const MIME_TYPE = "doctorQrCode";
           // dlLink.download = "doctorQrCodeImg";
           // dlLink.href = imageUrl;
           // dlLink.dataset.downloadurl = [
           //   MIME_TYPE,
           //   dlLink.download,
           //   dlLink.href,
           // ].join(":");
           // document.body.appendChild(dlLink);
           // dlLink.click();
           // document.body.removeChild(dlLink);
           document.body.appendChild(canvas);
         });
       }
     };

     useEffect(() => {
       let element = canvasRef.current;
       if (element) {
         let dom = element;			// 操作dom元素，只是浅拷贝，还是会改变原值
         console.log("dom", dom);
         ReactDom.findDOMNode(dom).style.visibility = "visible";
         printDocument(dom);
       }
     });
   ```

   在点击事件中：

   ```
   <button onclick={printDocument(canvasRef.current)}>保存</button>
   ```

5. 也可以通过 4 中注释的代码通过 a 标签的 click 方法实现自动下载。在 pc 端可以自动下载，手机端需要通过浏览器下载。

http://tool.chinaz.com/tools/imgtobase 可以查看 base64 编码的图片

```
{
        allowTaint: true,		//解决图片跨域
        scale: 1,				//解决scroll截图不全
        height: element.scrollHeight,
        windowHeight: element.scrollHeight,
      }
```

解决图片跨域和 scroll 出现滚动条超出页面截图不全的问题

##### 还可以使用 react-native-view-shot 插件，这个插件是将页面截图，导入后遇到问题不能使用，待解决尝试。

测试后发现 html2canvas 转换的图片在 IOS 端上方会出现空白部分，无法解决后使用 dom-to-image 实现。

解决 IOS 上方空白需要将 html2canvas 回退版本

```
yarn add html2canvas@1.0.0-rc.3
```

这个版本可以正常显示

##### 使用 dom-to-image 实现图片转化

1. 首先安装依赖；

   ```
   yarn add dom-to-image
   ```

2. 进行图片转化；

   ```
    domtoimage.toPng(element).then((dataUrl: any) => {
          let img = new Image();
          img.src = dataUrl;
          console.log("dataUrl", img.src);
          setImageUrl(dataUrl);
          document.body.appendChild(img);
          ReactDom.findDOMNode(element).style.visibility = "hidden";
        }).catch((error: any) => {
          console.error("oops, something went wrong!", error);
        });
   ```

这种方式的清晰度不如 html2canvas。
