### react项目中前端实现将某页面转化为base64格式图片

##### 使用html2canvas插件将页面dom节点转化为图片格式。

1. 安装html2canvas插件

   ```yarn命令
   yarn add html2canvas
   ```

2. 在页面中引用

   ```html
   import html2canvas from "html2canvas";
   ```

3. 使用useRef钩子函数获取dom对象

   ```
   const canvasRef = useRef();
   ```

     在想要转化的最外层dom对象添加ref，如：

   ```
   <div ref={canvasRef} className=...>
   	...
   </div>
   ```

4. 在页面加载时或按钮触发将页面转化

   页面加载时用useEffect钩子实现

   ```
   const printDocument = (domElement: any) => {
       if (domEelement) {
         html2canvas(domElement,{
           allowTaint: true,		//解决图片跨域
           scale: 1,				//解决scroll截图不全
           height: element.scrollHeight,
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

5. 也可以通过4中注释的代码通过a标签的click方法实现自动下载。在pc端可以自动下载，手机端需要通过浏览器下载。



http://tool.chinaz.com/tools/imgtobase       可以查看base64编码的图片

```
{
        allowTaint: true,		//解决图片跨域
        scale: 1,				//解决scroll截图不全
        height: element.scrollHeight,
        windowHeight: element.scrollHeight,
      }
```

解决图片跨域和scroll出现滚动条超出页面截图不全的问题

##### 还可以使用react-native-view-shot插件，这个插件是将页面截图，导入后遇到问题不能使用，待解决尝试。



