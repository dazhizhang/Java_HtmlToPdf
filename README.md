# convertHtmlToPdf

实际测试方法二可用。
来源：http://swordshadow.iteye.com/blog/1983935

itext实现HTML转pdf网上有很多实现方案，可惜大部分没有提供完整的源码下载，而有的方案却随着itext API的升级而失效，在此记录一下解决此问题的过程。
 
方法一：
最初是在老外的网站看到 http://hmkcode.com/itext-html-to-pdf-using-java/ 
Java代码  收藏代码
// step 1  
            Document document = new Document();  
        // step 2  
            PdfWriter writer = PdfWriter.getInstance(document, new FileOutputStream("pdf.pdf"));  
        // step 3  
        document.open();  
        // step 4  
        XMLWorkerHelper.getInstance().parseXHtml(writer, document,  
                new FileInputStream("index.html"));          
        //step 5  
         document.close();  
  
        System.out.println( "PDF Created!" );  
Maven构建对应的版本  关于eclipse配置maven，可以参考此文
Xml代码  收藏代码
<dependency>  
          <groupId>com.itextpdf</groupId>  
                <artifactId>itextpdf</artifactId>  
                <version>5.4.2</version>  
        </dependency>  
        <dependency>  
                <groupId>com.itextpdf.tool</groupId>  
                <artifactId>xmlworker</artifactId>  
                <version>5.4.1</version>  
        </dependency>  
 
 
 最简单的方式，HTML支持度很好，可惜不支持中文 源码地址：https://github.com/hmkcode/Java/blob/master/itext-java-html-pdf
 
 方法二：
使用的jar包：itext-2.0.8.jar  core-render.jar
 
App.java
Java代码  收藏代码
/** 
 *  
 * @author LJS 
 *  
 */  
public class App {  
    public void createPdf() throws Exception {  
        // step 1  
        String inputFile = "index.html";  
        String url = new File(inputFile).toURI().toURL().toString();  
        String outputFile = "index.pdf";  
        System.out.println(url);  
        // step 2  
        OutputStream os = new FileOutputStream(outputFile);  
        org.xhtmlrenderer.pdf.ITextRenderer renderer = new ITextRenderer();  
        renderer.setDocument(url);  
  
        // step 3 解决中文支持  
        org.xhtmlrenderer.pdf.ITextFontResolver fontResolver = renderer  
                .getFontResolver();  
        fontResolver.addFont("c:/Windows/Fonts/simsun.ttc", BaseFont.IDENTITY_H,     
                BaseFont.NOT_EMBEDDED);  
  
        renderer.layout();  
        renderer.createPDF(os);  
        os.close();  
          
        System.out.println("create pdf done!!");  
    }  
          
  
    public static void main(String[] args) throws Exception {  
        App app = new App();  
        app.createPdf();  
    }  
  
}  
注意指定中文字体
要转换的HTML
index.html
Html代码  收藏代码
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd ">       
<html xmlns="http://www.w3.org/1999/xhtml ">       
<head>       
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />       
<title>itext-zh-cn</title>       
<style type="text/css">           
body {       
            font-family: SimSun;  
}       
</style></head>       
       
<body>  
<p align="left" >OK，支持中文了:)</p>  
  
</body>       
</html>   
 同样也要指定中文字体，区分大小写
 运行程序，转换结果：


（字体样式和大家熟知的宋体不同，因为我替换了系统默认的宋体，pdf查看工具推荐PDF-XChange Viewer） 
 
 
pdf样式修改为A4 （ Document doc = new Document(PageSize.A4.rotate());）
在index.html中添加
Html代码  收藏代码
<style type="text/css">   
@page{ size: 11.69in 8.27in;}  
...  
</style>  
 
 
注意：无论哪种方式的Html格式转换pdf，对于html源文件要求是语法严格的；方法二支持基本的CSS样式，可以调整出合适的HTML模板。
 
大家有更好的方法，欢迎交流{#emotions_dlg.laughing}
 
其他：itext添加图片方法：实际应用中，应该与生成pdf合成一步提升性能
Java代码  收藏代码
public static void addImg(String fm) throws Exception {  
    PdfReader reader = new PdfReader("temp.pdf");  
    PdfStamper stamp = new PdfStamper(reader,new FileOutputStream("model.pdf"));  
    Image img = Image.getInstance("code.png");  //使用png格式  
    img.setAlignment(Image.LEFT | Image.TEXTWRAP);  
    img.setBorderWidth(10);  
    img.setAbsolutePosition(420, 240);  
    img.scaleToFit(1000, 60);// 大小  
    PdfContentByte over = stamp.getUnderContent(1); // overCount 与underCount   
    over.addImage(img);  
    stamp.close();  
    reader.close();  
    }  
  itext 版本号众多，可以在gerpcode查找其所有的版本
