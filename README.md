# How To Use Http Client ?? :)
About Http Client Tools.

HttpClient:是一个接口

首先需要先创建一个DefaultHttpClient的实例

HttpClient httpClient=new DefaultHttpClient();

发送GET请求:

先创建一个HttpGet对象,传入目标的网络地址,然后调用HttpClient的execute()方法即可:

HttpGet HttpGet=new HttpGet(“http://www.baidu.com”);

httpClient.execute(httpGet);

发送POST请求:

创建一个HttpPost对象,传入目标的网络地址:

HttpPost httpPost=new HttpPost(“http://www.baidu.com”);

通过一个NameValuePair集合来存放待提交的参数,并将这个参数集合传入到一个UrlEncodedFormEntity中,然后调用HttpPost的setEntity()方法将构建好的UrlEncodedFormEntity传入:

List<NameValuePair>params=newArrayList<NameValuePair>();

Params.add(new BasicNameValuePair(“username”,”admin”));

Params.add(new BasicNameValuePair(“password”,”123456”));

UrlEncodedFormEntity entity=newUrlEncodedFormEntity(params,”utf-8”);

httpPost.setEntity(entity);

调用HttpClient的execute()方法,并将HttpPost对象传入即可:

HttpClient.execute(HttpPost);

执行execute()方法之后会返回一个HttpResponse对象,服务器所返回的所有信息就保护在HttpResponse里面.

先取出服务器返回的状态码,如果等于200就说明请求和响应都成功了:

If(httpResponse.getStatusLine().getStatusCode()==200){

//请求和响应都成功了

HttpEntityentity=HttpResponse.getEntity();//调用getEntity()方法获取到一个HttpEntity实例

Stringresponse=EntityUtils.toString(entity,”utf-8”);//用EntityUtils.toString()这个静态方法将HttpEntity转换成字符串,防止服务器返回的数据带有中文,所以在转换的时候将字符集指定成utf-8就可以了

}

Http协议的重要性相信不用我多说了，HttpClient相比传统JDK自带的URLConnection，增加了易用性和灵活性（具体区别，日后我们再讨论），它不仅是客户端发送Http请求变得容易，而且也方便了开发人员测试接口（基于Http协议的），即提高了开发的效率，也方便提高代码的健壮性。因此熟练掌握HttpClient是很重要的必修内容，掌握HttpClient后，相信对于Http协议的了解会更加深入。

一、简介
HttpClient是Apache Jakarta Common下的子项目，用来提供高效的、最新的、功能丰富的支持HTTP协议的客户端编程工具包，并且它支持HTTP协议最新的版本和建议。HttpClient已经应用在很多的项目中，比如Apache Jakarta上很著名的另外两个开源项目Cactus和HTMLUnit都使用了HttpClient。

下载地址: http://hc.apache.org/downloads.cgi

二、特性
1. 基于标准、纯净的Java语言。实现了Http1.0和Http1.1

2. 以可扩展的面向对象的结构实现了Http全部的方法（GET, POST, PUT, DELETE, HEAD, OPTIONS, and TRACE）。

3. 支持HTTPS协议。

4. 通过Http代理建立透明的连接。

5. 利用CONNECT方法通过Http代理建立隧道的https连接。

6. Basic, Digest, NTLMv1, NTLMv2, NTLM2 Session, SNPNEGO/Kerberos认证方案。

7. 插件式的自定义认证方案。

8. 便携可靠的套接字工厂使它更容易的使用第三方解决方案。

9. 连接管理器支持多线程应用。支持设置最大连接数，同时支持设置每个主机的最大连接数，发现并关闭过期的连接。

10. 自动处理Set-Cookie中的Cookie。

11. 插件式的自定义Cookie策略。

12. Request的输出流可以避免流中内容直接缓冲到socket服务器。

13. Response的输入流可以有效的从socket服务器直接读取相应内容。

14. 在http1.0和http1.1中利用KeepAlive保持持久连接。

15. 直接获取服务器发送的response code和 headers。

16. 设置连接超时的能力。

17. 实验性的支持http1.1 response caching。

18. 源代码基于Apache License 可免费获取。
三、使用方法
使用HttpClient发送请求、接收响应很简单，一般需要如下几步即可。
1. 创建HttpClient对象。

2. 创建请求方法的实例，并指定请求URL。如果需要发送GET请求，创建HttpGet对象；如果需要发送POST请求，创建HttpPost对象。

3. 如果需要发送请求参数，可调用HttpGet、HttpPost共同的setParams(HetpParams params)方法来添加请求参数；对于HttpPost对象而言，也可调用setEntity(HttpEntity entity)方法来设置请求参数。

4. 调用HttpClient对象的execute(HttpUriRequest request)发送请求，该方法返回一个HttpResponse。

5. 调用HttpResponse的getAllHeaders()、getHeaders(String name)等方法可获取服务器的响应头；调用HttpResponse的getEntity()方法可获取HttpEntity对象，该对象包装了服务器的响应内容。程序可通过该对象获取服务器的响应内容。

6. 释放连接。无论执行方法是否成功，都必须释放连接

四、实例
[java]  view plain copy 在CODE上查看代码片 派生到我的代码片
package com.test;  
  
import java.io.File;  
import java.io.FileInputStream;  
import java.io.IOException;  
import java.io.UnsupportedEncodingException;  
import java.security.KeyManagementException;  
import java.security.KeyStore;  
import java.security.KeyStoreException;  
import java.security.NoSuchAlgorithmException;  
import java.security.cert.CertificateException;  
import java.util.ArrayList;  
import java.util.List;  
  
import javax.net.ssl.SSLContext;  
  
import org.apache.http.HttpEntity;  
import org.apache.http.NameValuePair;  
import org.apache.http.ParseException;  
import org.apache.http.client.ClientProtocolException;  
import org.apache.http.client.entity.UrlEncodedFormEntity;  
import org.apache.http.client.methods.CloseableHttpResponse;  
import org.apache.http.client.methods.HttpGet;  
import org.apache.http.client.methods.HttpPost;  
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;  
import org.apache.http.conn.ssl.SSLContexts;  
import org.apache.http.conn.ssl.TrustSelfSignedStrategy;  
import org.apache.http.entity.ContentType;  
import org.apache.http.entity.mime.MultipartEntityBuilder;  
import org.apache.http.entity.mime.content.FileBody;  
import org.apache.http.entity.mime.content.StringBody;  
import org.apache.http.impl.client.CloseableHttpClient;  
import org.apache.http.impl.client.HttpClients;  
import org.apache.http.message.BasicNameValuePair;  
import org.apache.http.util.EntityUtils;  
import org.junit.Test;  
  
public class HttpClientTest {  
  
    @Test  
    public void jUnitTest() {  
        get();  
    }  
  
    /** 
     * HttpClient连接SSL 
     */  
    public void ssl() {  
        CloseableHttpClient httpclient = null;  
        try {  
            KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());  
            FileInputStream instream = new FileInputStream(new File("d:\\tomcat.keystore"));  
            try {  
                // 加载keyStore d:\\tomcat.keystore    
                trustStore.load(instream, "123456".toCharArray());  
            } catch (CertificateException e) {  
                e.printStackTrace();  
            } finally {  
                try {  
                    instream.close();  
                } catch (Exception ignore) {  
                }  
            }  
            // 相信自己的CA和所有自签名的证书  
            SSLContext sslcontext = SSLContexts.custom().loadTrustMaterial(trustStore, new TrustSelfSignedStrategy()).build();  
            // 只允许使用TLSv1协议  
            SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext, new String[] { "TLSv1" }, null,  
                    SSLConnectionSocketFactory.BROWSER_COMPATIBLE_HOSTNAME_VERIFIER);  
            httpclient = HttpClients.custom().setSSLSocketFactory(sslsf).build();  
            // 创建http请求(get方式)  
            HttpGet httpget = new HttpGet("https://localhost:8443/myDemo/Ajax/serivceJ.action");  
            System.out.println("executing request" + httpget.getRequestLine());  
            CloseableHttpResponse response = httpclient.execute(httpget);  
            try {  
                HttpEntity entity = response.getEntity();  
                System.out.println("----------------------------------------");  
                System.out.println(response.getStatusLine());  
                if (entity != null) {  
                    System.out.println("Response content length: " + entity.getContentLength());  
                    System.out.println(EntityUtils.toString(entity));  
                    EntityUtils.consume(entity);  
                }  
            } finally {  
                response.close();  
            }  
        } catch (ParseException e) {  
            e.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        } catch (KeyManagementException e) {  
            e.printStackTrace();  
        } catch (NoSuchAlgorithmException e) {  
            e.printStackTrace();  
        } catch (KeyStoreException e) {  
            e.printStackTrace();  
        } finally {  
            if (httpclient != null) {  
                try {  
                    httpclient.close();  
                } catch (IOException e) {  
                    e.printStackTrace();  
                }  
            }  
        }  
    }  
  
    /** 
     * post方式提交表单（模拟用户登录请求） 
     */  
    public void postForm() {  
        // 创建默认的httpClient实例.    
        CloseableHttpClient httpclient = HttpClients.createDefault();  
        // 创建httppost    
        HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceJ.action");  
        // 创建参数队列    
        List<namevaluepair> formparams = new ArrayList<namevaluepair>();  
        formparams.add(new BasicNameValuePair("username", "admin"));  
        formparams.add(new BasicNameValuePair("password", "123456"));  
        UrlEncodedFormEntity uefEntity;  
        try {  
            uefEntity = new UrlEncodedFormEntity(formparams, "UTF-8");  
            httppost.setEntity(uefEntity);  
            System.out.println("executing request " + httppost.getURI());  
            CloseableHttpResponse response = httpclient.execute(httppost);  
            try {  
                HttpEntity entity = response.getEntity();  
                if (entity != null) {  
                    System.out.println("--------------------------------------");  
                    System.out.println("Response content: " + EntityUtils.toString(entity, "UTF-8"));  
                    System.out.println("--------------------------------------");  
                }  
            } finally {  
                response.close();  
            }  
        } catch (ClientProtocolException e) {  
            e.printStackTrace();  
        } catch (UnsupportedEncodingException e1) {  
            e1.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
            // 关闭连接,释放资源    
            try {  
                httpclient.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    /** 
     * 发送 post请求访问本地应用并根据传递参数不同返回不同结果 
     */  
    public void post() {  
        // 创建默认的httpClient实例.    
        CloseableHttpClient httpclient = HttpClients.createDefault();  
        // 创建httppost    
        HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceJ.action");  
        // 创建参数队列    
        List<namevaluepair> formparams = new ArrayList<namevaluepair>();  
        formparams.add(new BasicNameValuePair("type", "house"));  
        UrlEncodedFormEntity uefEntity;  
        try {  
            uefEntity = new UrlEncodedFormEntity(formparams, "UTF-8");  
            httppost.setEntity(uefEntity);  
            System.out.println("executing request " + httppost.getURI());  
            CloseableHttpResponse response = httpclient.execute(httppost);  
            try {  
                HttpEntity entity = response.getEntity();  
                if (entity != null) {  
                    System.out.println("--------------------------------------");  
                    System.out.println("Response content: " + EntityUtils.toString(entity, "UTF-8"));  
                    System.out.println("--------------------------------------");  
                }  
            } finally {  
                response.close();  
            }  
        } catch (ClientProtocolException e) {  
            e.printStackTrace();  
        } catch (UnsupportedEncodingException e1) {  
            e1.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
            // 关闭连接,释放资源    
            try {  
                httpclient.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    /** 
     * 发送 get请求 
     */  
    public void get() {  
        CloseableHttpClient httpclient = HttpClients.createDefault();  
        try {  
            // 创建httpget.    
            HttpGet httpget = new HttpGet("http://www.baidu.com/");  
            System.out.println("executing request " + httpget.getURI());  
            // 执行get请求.    
            CloseableHttpResponse response = httpclient.execute(httpget);  
            try {  
                // 获取响应实体    
                HttpEntity entity = response.getEntity();  
                System.out.println("--------------------------------------");  
                // 打印响应状态    
                System.out.println(response.getStatusLine());  
                if (entity != null) {  
                    // 打印响应内容长度    
                    System.out.println("Response content length: " + entity.getContentLength());  
                    // 打印响应内容    
                    System.out.println("Response content: " + EntityUtils.toString(entity));  
                }  
                System.out.println("------------------------------------");  
            } finally {  
                response.close();  
            }  
        } catch (ClientProtocolException e) {  
            e.printStackTrace();  
        } catch (ParseException e) {  
            e.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
            // 关闭连接,释放资源    
            try {  
                httpclient.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    /** 
     * 上传文件 
     */  
    public void upload() {  
        CloseableHttpClient httpclient = HttpClients.createDefault();  
        try {  
            HttpPost httppost = new HttpPost("http://localhost:8080/myDemo/Ajax/serivceFile.action");  
  
            FileBody bin = new FileBody(new File("F:\\image\\sendpix0.jpg"));  
            StringBody comment = new StringBody("A binary file of some kind", ContentType.TEXT_PLAIN);  
  
            HttpEntity reqEntity = MultipartEntityBuilder.create().addPart("bin", bin).addPart("comment", comment).build();  
  
            httppost.setEntity(reqEntity);  
  
            System.out.println("executing request " + httppost.getRequestLine());  
            CloseableHttpResponse response = httpclient.execute(httppost);  
            try {  
                System.out.println("----------------------------------------");  
                System.out.println(response.getStatusLine());  
                HttpEntity resEntity = response.getEntity();  
                if (resEntity != null) {  
                    System.out.println("Response content length: " + resEntity.getContentLength());  
                }  
                EntityUtils.consume(resEntity);  
            } finally {  
                response.close();  
            }  
        } catch (ClientProtocolException e) {  
            e.printStackTrace();  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
            try {  
                httpclient.close();  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
package com.jia.networktest;


import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;


import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.NameValuePair;
import org.apache.http.client.HttpClient;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;


import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;


public class MainActivity extends Activity implements OnClickListener {


public static final int SHOW_RESPONSE = 0;
public static final int SHOW_HTTPCLIENT = 1;
private Button send_request;
private TextView responseText;
private Button btn_httpClient;
private Handler handler = new Handler() {
public void handleMessage(Message msg) {
switch (msg.what) {
case SHOW_RESPONSE:
String response = (String) msg.obj;
// 在这里进行UI操作,将结果显示到界面上
responseText.setText(response);
case SHOW_HTTPCLIENT:
String m_httpClient = (String) msg.obj;
// 在这里进行UI操作,将结果显示到界面上
responseText.setText(m_httpClient);
}
}
};


@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
send_request = (Button) findViewById(R.id.send_request);
responseText = (TextView) findViewById(R.id.response);
btn_httpClient = (Button) findViewById(R.id.HttpClient);
send_request.setOnClickListener(this);
btn_httpClient.setOnClickListener(this);
}


@Override
public void onClick(View v) {
if (v.getId() == R.id.send_request) {
sendRequestWithHttpURLConnection();
} else if (v.getId() == R.id.HttpClient) {
sendRequestWithHttpClient();
}


}


private void sendRequestWithHttpClient() {
new Thread(new Runnable() {


@Override
public void run() {
try {
HttpClient httpClient = new DefaultHttpClient();
HttpPost httpPost = new HttpPost("http://www.baidu.com");
List<NameValuePair> params = new ArrayList<NameValuePair>();
params.add(new BasicNameValuePair("username",
"yao_jiawei@hotmail.com"));
params.add(new BasicNameValuePair("password", "yaowentian"));
UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(
params, "utf-8");
httpPost.setEntity(urlEncodedFormEntity);


HttpResponse httpResponse = httpClient.execute(httpPost);
if (httpResponse.getStatusLine().getStatusCode() == 200) {
// 请求和响应都成功了
HttpEntity entity = httpResponse.getEntity();// 获取到一个HttpEntity实例
String response = EntityUtils.toString(entity, "utf-8");// 用EntityUtils.toString()这个方法将HttpEntity转换成字符串
Message message = new Message();
message.what = SHOW_HTTPCLIENT;
// 将服务器返回的结果存放到Message中
message.obj = response.toString();
handler.sendMessage(message);
}
} catch (Exception e) {
// TODO Auto-generated catch block
e.printStackTrace();
}


}
}).start();


}


private void sendRequestWithHttpURLConnection() {
// 开启线程来发起网络请求
new Thread(new Runnable() {


@Override
public void run() {
HttpURLConnection connection = null;
try {
URL url = new URL("http://www.baidu.com");
connection = (HttpURLConnection) url.openConnection();
connection.setRequestMethod("POST");
connection.setConnectTimeout(8000);
connection.setReadTimeout(8000);
DataOutputStream out = new DataOutputStream(connection
.getOutputStream());
out.writeBytes("username=yao_jiawei@hotmail.com&password=yaowentian");
InputStream in = connection.getInputStream();
// 下面对获取到的输入流进行读取
BufferedReader reader = new BufferedReader(
new InputStreamReader(in));
StringBuilder response = new StringBuilder();
String line;
while ((line = reader.readLine()) != null) {
response.append(line);
}
Message message = new Message();
message.what = SHOW_RESPONSE;
// 将服务器返回的结果存放到message中
message.obj = response.toString();
handler.sendMessage(message);
} catch (Exception e) {
// TODO Auto-generated catch block
e.printStackTrace();
} finally {
if (connection != null) {
connection.disconnect();
}
}


}
}).start();
}
}
首先要注意的有以下几点： 
1、httpclient连接后资源释放问题很重要，就跟我们用database connection要释放资源一样。 
2、https网站采用ssl加密传输，证书导入要注意。 
3、做这样的项目最好先了解下http协义，比如302,301,200,404返回代码的含义（这是最基本的）,cookie,session的机制。 
4、httpclient的redirect状态默认是自动的，这在很大程度上给开发者很大的方便（如一些授权获得cookie），但是有时要手动管理下，比如 
　　有时会遇到CircularRedirectException异常，出现这样的情况是因为返回的头文件中location值指向之前重复(端口号可以不同)地址，导致可能会出现死 
  循环递归重定向，这时可以手动关闭:method.setFollowRedirects(false) 
5、有的网站会先判别用户的请求是否是来自浏览器，如不是，则返回不正确的文本，所以用httpclient抓取信息时在头部加入如下信息： 
  header.put("User-Agent", "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; QQDownload 1.7; .NET CLR 1.1.4322; CIBA; .NET CLR 2.0.50727)"); 
6、当post请求提交数据时要改变默认编码，不然的话提交上去的数据会出现乱码。重写postMethod的setContentCharSet()方法就可以了： 





  
下面写一个通用类来处理request请求返回的文本: 
Java代码   收藏代码
/* 
 * HttpRequestProxy.java 
 * 
 * Created on November 3, 2008, 9:53 AM 
 */  
  
package cn.com.mozat.net;  
  
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStream;  
import java.io.InputStreamReader;  
import java.util.HashMap;  
import java.util.Iterator;  
import java.util.Map;  
import java.util.Set;  
  
import org.apache.commons.httpclient.Header;  
import org.apache.commons.httpclient.HttpClient;  
import org.apache.commons.httpclient.HttpException;  
import org.apache.commons.httpclient.HttpMethod;  
import org.apache.commons.httpclient.NameValuePair;  
import org.apache.commons.httpclient.SimpleHttpConnectionManager;  
import org.apache.commons.httpclient.methods.GetMethod;  
import org.apache.commons.httpclient.methods.PostMethod;  
  
import cn.com.mozat.exception.CustomException;  
  
/** 
 *  
 * @author bird  email:lihongfu-84@163.com 
 * 
 * 2008-11-4  09:49:48 
 */  
public class HttpRequestProxy{  
    //超时间隔  
    private static int connectTimeOut = 60000;  
 //让connectionmanager管理httpclientconnection时是否关闭连接  
    private static boolean alwaysClose = false;  
 //返回数据编码格式  
    private String encoding = "UTF-8";  
      
    private final HttpClient client = new HttpClient(new SimpleHttpConnectionManager(alwaysClose));  
   
    public HttpClient getHttpClient(){  
        return client;  
    }  
        
    /** 
     * 用法： 
     * HttpRequestProxy hrp = new HttpRequestProxy(); 
     * hrp.doRequest("http://www.163.com",null,null,"gbk"); 
     *  
     * @param url  请求的资源ＵＲＬ 
     * @param postData  POST请求时form表单封装的数据 没有时传null 
     * @param header   request请求时附带的头信息(header) 没有时传null 
     * @param encoding response返回的信息编码格式 没有时传null 
     * @return  response返回的文本数据 
     * @throws CustomException  
     */  
    public String doRequest(String url,Map postData,Map header,String encoding) throws CustomException{  
     String responseString = null;  
     //头部请求信息  
     Header[] headers = null;  
     if(header != null){  
      Set entrySet = header.entrySet();  
         int dataLength = entrySet.size();  
          headers= new Header[dataLength];  
         int i = 0;  
         for(Iterator itor = entrySet.iterator();itor.hasNext();){  
          Map.Entry entry = (Map.Entry)itor.next();  
          headers[i++] = new Header(entry.getKey().toString(),entry.getValue().toString());  
         }  
     }  
     //post方式  
        if(postData!=null){  
         PostMethod postRequest = new PostMethod(url.trim());  
         if(headers != null){  
          for(int i = 0;i < headers.length;i++){  
           postRequest.setRequestHeader(headers[i]);  
          }  
         }  
         Set entrySet = postData.entrySet();  
         int dataLength = entrySet.size();  
         NameValuePair[] params = new NameValuePair[dataLength];  
         int i = 0;  
         for(Iterator itor = entrySet.iterator();itor.hasNext();){  
          Map.Entry entry = (Map.Entry)itor.next();  
          params[i++] = new NameValuePair(entry.getKey().toString(),entry.getValue().toString());  
         }  
         postRequest.setRequestBody(params);  
         try {  
    responseString = this.executeMethod(postRequest,encoding);  
   } catch (CustomException e) {  
    throw e;  
   } finally{  
    postRequest.releaseConnection();  
   }  
        }  
      //get方式  
        if(postData == null){  
         GetMethod getRequest = new GetMethod(url.trim());  
         if(headers != null){  
          for(int i = 0;i < headers.length;i++){  
           getRequest.setRequestHeader(headers[i]);  
          }  
         }  
         try {  
    responseString = this.executeMethod(getRequest,encoding);  
   } catch (CustomException e) {  
                e.printStackTrace();  
    throw e;  
   }finally{  
    getRequest.releaseConnection();  
   }  
        }  
   
        return responseString;  
    }  
  
 private String executeMethod(HttpMethod request, String encoding) throws CustomException{  
  String responseContent = null;  
  InputStream responseStream = null;  
  BufferedReader rd = null;  
  try {  
   this.getHttpClient().executeMethod(request);  
   if(encoding != null){  
    responseStream = request.getResponseBodyAsStream();  
     rd = new BufferedReader(new InputStreamReader(responseStream,  
                      encoding));  
              String tempLine = rd.readLine();  
              StringBuffer tempStr = new StringBuffer();  
              String crlf=System.getProperty("line.separator");  
              while (tempLine != null)  
              {  
                  tempStr.append(tempLine);  
                  tempStr.append(crlf);  
                  tempLine = rd.readLine();  
              }  
              responseContent = tempStr.toString();  
   }else  
    responseContent = request.getResponseBodyAsString();  
             
   Header locationHeader = request.getResponseHeader("location");  
   //返回代码为302,301时，表示页面己经重定向，则重新请求location的url，这在  
   //一些登录授权取cookie时很重要  
   if (locationHeader != null) {  
             String redirectUrl = locationHeader.getValue();  
             this.doRequest(redirectUrl, null, null,null);  
         }  
  } catch (HttpException e) {  
   throw new CustomException(e.getMessage());  
  } catch (IOException e) {  
   throw new CustomException(e.getMessage());  
  
  } finally{  
   if(rd != null)  
    try {  
     rd.close();  
    } catch (IOException e) {  
     throw new CustomException(e.getMessage());  
    }  
    if(responseStream != null)  
     try {  
      responseStream.close();  
     } catch (IOException e) {  
      throw new CustomException(e.getMessage());  
  
     }  
  }  
  return responseContent;  
 }  
   
     
 /** 
  * 特殊请求数据,这样的请求往往会出现redirect本身而出现递归死循环重定向 
  * 所以单独写成一个请求方法 
  * 比如现在请求的url为：http://localhost:8080/demo/index.jsp 
  * 返回代码为302 头部信息中location值为:http://localhost:8083/demo/index.jsp 
  * 这时httpclient认为进入递归死循环重定向，抛出CircularRedirectException异常 
  * @param url 
  * @return 
  * @throws CustomException  
  */  
 public String doSpecialRequest(String url,int count,String encoding) throws CustomException{  
  String str = null;  
  InputStream responseStream = null;  
  BufferedReader rd = null;  
  GetMethod getRequest = new GetMethod(url);  
  //关闭httpclient自动重定向动能  
  getRequest.setFollowRedirects(false);  
  try {  
     
   this.client.executeMethod(getRequest);  
   Header header = getRequest.getResponseHeader("location");  
   if(header!= null){  
    //请求重定向后的ＵＲＬ，count同时加1  
    this.doSpecialRequest(header.getValue(),count+1, encoding);  
   }  
   //这里用count作为标志位，当count为0时才返回请求的ＵＲＬ文本,  
   //这样就可以忽略所有的递归重定向时返回文本流操作，提高性能  
   if(count == 0){  
    getRequest = new GetMethod(url);  
    getRequest.setFollowRedirects(false);  
    this.client.executeMethod(getRequest);  
    responseStream = getRequest.getResponseBodyAsStream();  
    rd = new BufferedReader(new InputStreamReader(responseStream,  
                      encoding));  
             String tempLine = rd.readLine();  
             StringBuffer tempStr = new StringBuffer();  
             String crlf=System.getProperty("line.separator");  
             while (tempLine != null)  
             {  
                 tempStr.append(tempLine);  
                 tempStr.append(crlf);  
                 tempLine = rd.readLine();  
             }  
             str = tempStr.toString();  
   }  
     
  } catch (HttpException e) {  
   throw new CustomException(e.getMessage());  
  } catch (IOException e) {  
   throw new CustomException(e.getMessage());  
  } finally{  
   getRequest.releaseConnection();  
   if(rd !=null)  
    try {  
     rd.close();  
    } catch (IOException e) {  
     throw new CustomException(e.getMessage());  
    }  
    if(responseStream !=null)  
     try {  
      responseStream.close();  
     } catch (IOException e) {  
      throw new CustomException(e.getMessage());  
     }  
  }  
  return str;  
 }  
   
   
   
   
 public static void main(String[] args) throws Exception{  
  HttpRequestProxy hrp = new HttpRequestProxy();  
   Map header = new HashMap();  
         header.put("User-Agent", "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; QQDownload 1.7; .NET CLR 1.1.4322; CIBA; .NET CLR 2.0.50727)");  
  String str = hrp.doRequest(  
    "http://www.cma-cgm.com/en/eBusiness/Tracking/Default.aspx?BolNumber=GZ2108827",  
     null, header,null);  
  System.out.println(str.contains("row_CRXU1587647"));  
//  System.out.println(str);  
 }  
     
}  
HttpClient 是 Apache Jakarta Common 下的子项目，可以用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。本文首先介绍 HTTPClient，然后根据作者实际工作经验给出了一些常见问题的解决方法。HTTP 协议可能是现在 Internet 上使用得最多、最重要的协议了，越来越多的 Java 应用程序需要直接通过 HTTP 协议来访问网络资源。虽然在 JDK 的 java.net 包中已经提供了访问 HTTP 协议的基本功能，但是对于大部分应用程序来说，JDK 库本身提供的功能还不够丰富和灵活。HttpClient 是 Apache Jakarta Common 下的子项目，用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。HttpClient 已经应用在很多的项目中，比如 Apache Jakarta 上很著名的另外两个开源项目 Cactus 和 HTMLUnit 都使用了 HttpClient。现在HttpClient最新版本为 HttpClient 4.0-beta2
[编辑本段]2.HttpClient 功能介绍
　　以下列出的是 HttpClient 提供的主要的功能，要知道更多详细的功能可以参见 HttpClient 的主页。
　　（1）实现了所有 HTTP 的方法（GET,POST,PUT,HEAD 等） 
　　（2）支持自动转向 
　　（3）支持 HTTPS 协议 
　　（4）支持代理服务器等
[编辑本段]3.HttpClient 基本功能的使用
　　(1) GET方法
　　使用 HttpClient 需要以下 6 个步骤：
　　1. 创建 HttpClient 的实例
　　2. 创建某种连接方法的实例，在这里是 GetMethod。在 GetMethod 的构造函数中传入待连接的地址
　　3. 调用第一步中创建好的实例的 execute 方法来执行第二步中创建好的 method 实例
　　4. 读 response
　　5. 释放连接。无论执行方法是否成功，都必须释放连接
　　6. 对得到后的内容进行处理
　　根据以上步骤，我们来编写用GET方法来取得某网页内容的代码。
　　大部分情况下 HttpClient 默认的构造函数已经足够使用。 HttpClient httpClient = new HttpClient();
　　创建GET方法的实例。在GET方法的构造函数中传入待连接的地址即可。用GetMethod将会自动处理转发过程，如果想要把自动处理转发过程去掉的话，可以调用方法setFollowRedirects(false)。 GetMethod getMethod = new GetMethod(".....");
　　调用实例httpClient的executeMethod方法来执行getMethod。由于是执行在网络上的程序，在运行executeMethod方法的时候，需要处理两个异常，分别是HttpException和IOException。引起第一种异常的原因主要可能是在构造getMethod的时候传入的协议不对，比如不小心将"http"写成"htp"，或者服务器端返回的内容不正常等，并且该异常发生是不可恢复的；第二种异常一般是由于网络原因引起的异常，对于这种异常 （IOException），HttpClient会根据你指定的恢复策略自动试着重新执行executeMethod方法。HttpClient的恢复策略可以自定义（通过实现接口HttpMethodRetryHandler来实现）。通过httpClient的方法setParameter设置你实现的恢复策略，本文中使用的是系统提供的默认恢复策略，该策略在碰到第二类异常的时候将自动重试3次。executeMethod返回值是一个整数，表示了执行该方法后服务器返回的状态码，该状态码能表示出该方法执行是否成功、需要认证或者页面发生了跳转（默认状态下GetMethod的实例是自动处理跳转的）等。 //设置成了默认的恢复策略，在发生异常时候将自动重试3次，在这里你也可以设置成自定义的恢复策略
　　getMethod.getParams().setParameter(HttpMethodParams.RETRY_HANDLER, 
　　new DefaultHttpMethodRetryHandler()); 
　　//执行getMethod
　　int statusCode = client.executeMethod(getMethod);
　　if (statusCode != HttpStatus.SC_OK) {
　　System.err.println("Method failed: " + getMethod.getStatusLine());
　　}
　　在返回的状态码正确后，即可取得内容。取得目标地址的内容有三种方法：第一种，getResponseBody，该方法返回的是目标的二进制的byte流；第二种，getResponseBodyAsString，这个方法返回的是String类型，值得注意的是该方法返回的String的编码是根据系统默认的编码方式，所以返回的String值可能编码类型有误，在本文的"字符编码"部分中将对此做详细介绍；第三种，getResponseBodyAsStream，这个方法对于目标地址中有大量数据需要传输是最佳的。在这里我们使用了最简单的getResponseBody方法。 byte[] responseBody = method.getResponseBody();
　　释放连接。无论执行方法是否成功，都必须释放连接。 method.releaseConnection();
　　处理内容。在这一步中根据你的需要处理内容，在例子中只是简单的将内容打印到控制台。 System.out.println(new String(responseBody));
　　下面是程序的完整代码，这些代码也可在附件中的test.GetSample中找到。
　　package test;
　　import java.io.IOException;
　　import org.apache.commons.httpclient.*;
　　import org.apache.commons.httpclient.methods.GetMethod;
　　import org.apache.commons.httpclient.params.HttpMethodParams;
　　public class GetSample{
　　public static void main(String[] args) {
　　//构造HttpClient的实例
　　HttpClient httpClient = new HttpClient();
　　//创建GET方法的实例
　　GetMethod getMethod = new GetMethod("...");
　　//使用系统提供的默认的恢复策略
　　getMethod.getParams().setParameter(HttpMethodParams.RETRY_HANDLER,
　　new DefaultHttpMethodRetryHandler());
　　try {
　　//执行getMethod
　　int statusCode = httpClient.executeMethod(getMethod);
　　if (statusCode != HttpStatus.SC_OK) {
　　System.err.println("Method failed: "
　　+ getMethod.getStatusLine());
　　}
　　//读取内容 
　　byte[] responseBody = getMethod.getResponseBody();
　　//处理内容
　　System.out.println(new String(responseBody));
　　} catch (HttpException e) {
　　//发生致命的异常，可能是协议不对或者返回的内容有问题
　　System.out.println("Please check your provided http address!");
　　e.printStackTrace();
　　} catch (IOException e) {
　　//发生网络异常
　　e.printStackTrace();
　　} finally {
　　//释放连接
　　getMethod.releaseConnection();
　　}
　　}
　　}
　　（2）POST方法
　　根据RFC2616，对POST的解释如下：POST方法用来向目的服务器发出请求，要求它接受被附在请求后的实体，并把它当作请求队列（Request-Line）中请求URI所指定资源的附加新子项。POST被设计成用统一的方法实现下列功能：
　　对现有资源的注释（Annotation of existing resources） 
　　向电子公告栏、新闻组，邮件列表或类似讨论组发送消息 
　　提交数据块，如将表单的结果提交给数据处理过程 
　　通过附加操作来扩展数据库 
　　调用HttpClient中的PostMethod与GetMethod类似，除了设置PostMethod的实例与GetMethod有些不同之外，剩下的步骤都差不多。在下面的例子中，省去了与GetMethod相同的步骤，只说明与上面不同的地方，并以登录清华大学BBS为例子进行说明。
　　构造PostMethod之前的步骤都相同，与GetMethod一样，构造PostMethod也需要一个URI参数。在创建了PostMethod的实例之后，需要给method实例填充表单的值，在BBS的登录表单中需要有两个域，第一个是用户名（域名叫id），第二个是密码（域名叫passwd）。表单中的域用类NameValuePair来表示，该类的构造函数第一个参数是域名，第二参数是该域的值；将表单所有的值设置到PostMethod中用方法setRequestBody。另外由于BBS登录成功后会转向另外一个页面，但是HttpClient对于要求接受后继服务的请求，比如POST和PUT，不支持自动转发，因此需要自己对页面转向做处理。具体的页面转向处理请参见下面的"自动转向"部分。代码如下：
　　String url = "....";
　　PostMethod postMethod = new PostMethod(url);
　　// 填入各个表单域的值
　　NameValuePair[] data = { new NameValuePair("id", "youUserName"), 
　　new NameValuePair("passwd", "yourPwd") };
　　// 将表单的值放入postMethod中
　　postMethod.setRequestBody(data);
　　// 执行postMethod
　　int statusCode = httpClient.executeMethod(postMethod);
　　// HttpClient对于要求接受后继服务的请求，象POST和PUT等不能自动处理转发
　　// 301或者302
　　if (statusCode == HttpStatus.SC_MOVED_PERMANENTLY || 
　　statusCode == HttpStatus.SC_MOVED_TEMPORARILY) {
　　// 从头中取出转向的地址
　　Header locationHeader = postMethod.getResponseHeader("location");
　　String location = null;
　　if (locationHeader != null) {
　　location = locationHeader.getValue();
　　System.out.println("The page was redirected to:" + location);
　　} else {
　　System.err.println("Location field value is null.");
　　}
　　return;
　　}
[编辑本段]4 使用HttpClient过程中常见的一些问题
　　下面介绍在使用HttpClient过程中常见的一些问题。
　　字符编码
　　某目标页的编码可能出现在两个地方，第一个地方是服务器返回的http头中，另外一个地方是得到的html/xml页面中。
　　在http头的Content-Type字段可能会包含字符编码信息。例如可能返回的头会包含这样子的信息：Content-Type: text/html; charset=UTF-8。这个头信息表明该页的编码是UTF-8，但是服务器返回的头信息未必与内容能匹配上。比如对于一些双字节语言国家，可能服务器返回的编码类型是UTF-8，但真正的内容却不是UTF-8编码的，因此需要在另外的地方去得到页面的编码信息；但是如果服务器返回的编码不是UTF-8，而是具体的一些编码，比如gb2312等，那服务器返回的可能是正确的编码信息。通过method对象的getResponseCharSet()方法就可以得到http头中的编码信息。 
　　对于象xml或者html这样的文件，允许作者在页面中直接指定编码类型。比如在html中会有<meta http-equiv="Content-Type" content="text/html; charset=gb2312"/>这样的标签；或者在xml中会有<?xml version="1.0" encoding="gb2312"?>这样的标签，在这些情况下，可能与http头中返回的编码信息冲突，需要用户自己判断到底那种编码类型应该是真正的编码。 
　　自动转向
　　根据RFC2616中对自动转向的定义，主要有两种：301和302。301表示永久的移走（Moved Permanently），当返回的是301，则表示请求的资源已经被移到一个固定的新地方，任何向该地址发起请求都会被转到新的地址上。302表示暂时的转向，比如在服务器端的servlet程序调用了sendRedirect方法，则在客户端就会得到一个302的代码，这时服务器返回的头信息中location的值就是sendRedirect转向的目标地址。
　　HttpClient支持自动转向处理，但是象POST和PUT方式这种要求接受后继服务的请求方式，暂时不支持自动转向，因此如果碰到POST方式提交后返回的是301或者302的话需要自己处理。就像刚才在POSTMethod中举的例子：如果想进入登录BBS后的页面，必须重新发起登录的请求，请求的地址可以在头字段location中得到。不过需要注意的是，有时候location返回的可能是相对路径，因此需要对location返回的值做一些处理才可以发起向新地址的请求。
　　另外除了在头中包含的信息可能使页面发生重定向外，在页面中也有可能会发生页面的重定向。引起页面自动转发的标签是：<meta http-equiv="refresh" content="5; url=....">。如果你想在程序中也处理这种情况的话得自己分析页面来实现转向。需要注意的是，在上面那个标签中url的值也可以是一个相对地址，如果是这样的话，需要对它做一些处理后才可以转发。
　　处理HTTPS协议
　　HttpClient提供了对SSL的支持，在使用SSL之前必须安装JSSE。在Sun提供的1.4以后的版本中，JSSE已经集成到JDK中，如果你使用的是JDK1.4以前的版本则必须安装JSSE。JSSE不同的厂家有不同的实现。下面介绍怎么使用HttpClient来打开Https连接。这里有两种方法可以打开https连接，第一种就是得到服务器颁发的证书，然后导入到本地的keystore中；另外一种办法就是通过扩展HttpClient的类来实现自动接受证书。
　　方法1，取得证书，并导入本地的keystore：
　　安装JSSE （如果你使用的JDK版本是1.4或者1.4以上就可以跳过这一步）。本文以IBM的JSSE为例子说明。先到IBM网站上下载JSSE的安装包。然后解压开之后将ibmjsse.jar包拷贝到<java-home>\lib\ext\目录下。 
　　取得并且导入证书。证书可以通过IE来获得： 
　　1． 用IE打开需要连接的https网址，会弹出如下对话框：
　　2． 单击"View Certificate"，在弹出的对话框中选择"Details"，然后再单击"Copy to File"，根据提供的向导生成待访问网页的证书文件
　　3． 向导第一步，欢迎界面，直接单击"Next"，
　　4． 向导第二步，选择导出的文件格式，默认，单击"Next"，
　　5． 向导第三步，输入导出的文件名，输入后，单击"Next"，
　　6． 向导第四步，单击"Finish"，完成向导
　　7． 最后弹出一个对话框，显示导出成功
　　用keytool工具把刚才导出的证书倒入本地keystore。Keytool命令在<java-home>\bin\下，打开命令行窗口，并到<java-home>\lib\security\目录下，运行下面的命令：
　　keytool -import -noprompt -keystore cacerts -storepass changeit -alias yourEntry1 -file your.cer
　　其中参数alias后跟的值是当前证书在keystore中的唯一标识符，但是大小写不区分；参数file后跟的是刚才通过IE导出的证书所在的路径和文件名；如果你想删除刚才导入到keystore的证书，可以用命令：
　　keytool -delete -keystore cacerts -storepass changeit -alias yourEntry1
　　写程序访问https地址。如果想测试是否能连上https，只需要稍改一下GetSample例子，把请求的目标变成一个https地址。 
　　GetMethod getMethod = new GetMethod("your url");
　　运行该程序可能出现的问题：
　　1. 抛出异常java.net.SocketException: Algorithm SSL not available。出现这个异常可能是因为没有加JSSEProvider，如果用的是IBM的JSSE Provider，在程序中加入这样的一行：
　　if(Security.getProvider("com.ibm.jsse.IBMJSSEProvider") == null)
　　Security.addProvider(new IBMJSSEProvider());
　　或者也可以打开<java-home>\lib\security\java.security，在行
　　security.provider.1=sun.security.provider.Sun
　　security.provider.2=com.ibm.crypto.provider.IBMJCE
　　后面加入security.provider.3=com.ibm.jsse.IBMJSSEProvider
　　2. 抛出异常java.net.SocketException: SSL implementation not available。出现这个异常可能是你没有把ibmjsse.jar拷贝到<java-home>\lib\ext\目录下。
　　3. 抛出异常javax.net.ssl.SSLHandshakeException: unknown certificate。出现这个异常表明你的JSSE应该已经安装正确，但是可能因为你没有把证书导入到当前运行JRE的keystore中，请按照前面介绍的步骤来导入你的证书。
　　方法２，扩展HttpClient类实现自动接受证书
　　因为这种方法自动接收所有证书，因此存在一定的安全问题，所以在使用这种方法前请仔细考虑您的系统的安全需求。具体的步骤如下：
　　提供一个自定义的socket factory（test.MySecureProtocolSocketFactory）。这个自定义的类必须实现接口org.apache.commons.httpclient.protocol.SecureProtocolSocketFactory，在实现接口的类中调用自定义的X509TrustManager(test.MyX509TrustManager)，这两个类可以在随本文带的附件中得到 
　　创建一个org.apache.commons.httpclient.protocol.Protocol的实例，指定协议名称和默认的端口号 Protocol myhttps = new Protocol("https", new MySecureProtocolSocketFactory (), 443);
　　注册刚才创建的https协议对象 Protocol.registerProtocol("https ", myhttps);
　　然后按照普通编程方式打开https的目标地址，代码请参见test.NoCertificationHttpsGetSample
[编辑本段]5 处理代理服务器
　　HttpClient中使用代理服务器非常简单，调用HttpClient中setProxy方法就可以，方法的第一个参数是代理服务器地址，第二个参数是端口号。另外HttpClient也支持SOCKS代理。
　　httpClient.getHostConfiguration().setProxy(hostName,port);
一般的情况下我们都是使用IE或者Navigator浏览器来访问一个WEB服务器，用来浏览页面查看信息或者提交一些数据等等。所访问的这些页面有的仅仅是一些普通的页面，有的需要用户登录后方可使用，或者需要认证以及是一些通过加密方式传输，例如HTTPS。目前我们使用的浏览器处理这些情况都不会构成问题。不过你可能在某些时候需要通过程序来访问这样的一些页面，比如从别人的网页中“偷”一些数据；利用某些站点提供的页面来完成某种功能，例如说我们想知道某个手机号码的归属地而我们自己又没有这样的数据，因此只好借助其他公司已有的网站来完成这个功能，这个时候我们需要向网页提交手机号码并从返回的页面中解析出我们想要的数据来。如果对方仅仅是一个很简单的页面，那我们的程序会很简单，本文也就没有必要大张旗鼓的在这里浪费口舌。但是考虑到一些服务授权的问题，很多公司提供的页面往往并不是可以通过一个简单的URL就可以访问的，而必须经过注册然后登录后方可使用提供服务的页面，这个时候就涉及到COOKIE问题的处理。我们知道目前流行的动态网页技术例如ASP、JSP无不是通过COOKIE来处理会话信息的。为了使我们的程序能使用别人所提供的服务页面，就要求程序首先登录后再访问服务页面，这过程就需要自行处理cookie，想想当你用java.NET.HttpURLConnection来完成这些功能时是多么恐怖的事情啊！况且这仅仅是我们所说的顽固的WEB服务器中的一个很常见的“顽固”！再有如通过HTTP来上传文件呢？不需要头疼，这些问题有了“它”就很容易解决了！ 

我们不可能列举所有可能的顽固，我们会针对几种最常见的问题进行处理。当然了，正如前面说到的，如果我们自己使用java.Net.HttpURLConnection来搞定这些问题是很恐怖的事情，因此在开始之前我们先要介绍一下一个开放源码的项目，这个项目就是Apache开源组织中的httpclient，它隶属于Jakarta的commons项目，目前的版本是2.0RC2。commons下本来已经有一个net的子项目，但是又把httpclient单独提出来，可见http服务器的访问绝非易事。 

Commons-httpclient项目就是专门设计来简化HTTP客户端与服务器进行各种通讯编程。通过它可以让原来很头疼的事情现在轻松的解决，例如你不再管是HTTP或者HTTPS的通讯方式，告诉它你想使用HTTPS方式，剩下的事情交给httpclient替你完成。本文会针对我们在编写HTTP客户端程序时经常碰到的几个问题进行分别介绍如何使用httpclient来解决它们，为了让读者更快的熟悉这个项目我们最开始先给出一个简单的例子来读取一个网页的内容，然后循序渐进解决掉前进中的所有问题。 
1． 读取网页(HTTP/HTTPS)内容 
下面是我们给出的一个简单的例子用来访问某个页面 
 
*    
 * Created on 2003-12-14 by skydong 
 */    
   
package http.demo;    
import java.io.IOException;    
import org.apache.commons.httpclient.*;    
import org.apache.commons.httpclient.methods.*;    
/** *//**   
 * 最简单的HTTP客户端,用来演示通过GET或者POST方式访问某个页面   
 * @author skydong 
 */   
public class SimpleClient ...{    
    public static void main(String[] args) throws IOException    
    ...{    
        HttpClient client = new HttpClient();       
        //设置代理服务器地址和端口         
     //client.getHostConfiguration().setProxy("proxy_host_addr",proxy_port);    
        //使用GET方法，如果服务器需要通过HTTPS连接，那只需要将下面URL中的http换成https    
        HttpMethod method = new GetMethod("http://java.sun.com";);     
        //使用POST方法    
     //HttpMethod method = new PostMethod("http://java.sun.com";);     
        client.executeMethod(method);    
        //打印服务器返回的状态    
     System.out.println(method.getStatusLine());    
       //打印返回的信息    
     System.out.println(method.getResponseBodyAsString());    
       //释放连接    
     method.releaseConnection();    
    }    
}    
 


在这个例子中首先创建一个HTTP客户端(HttpClient)的实例，然后选择提交的方法是GET或者POST，最后在HttpClient实例上执行提交的方法，最后从所选择的提交方法中读取服务器反馈回来的结果。这就是使用HttpClient的基本流程。其实用一行代码也就可以搞定整个请求的过程，非常的简单！ 

2． 以GET或者POST方式向网页提交参数 
其实前面一个最简单的示例中我们已经介绍了如何使用GET或者POST方式来请求一个页面，本小节与之不同的是多了提交时设定页面所需的参数，我们知道如果是GET的请求方式，那么所有参数都直接放到页面的URL后面用问号与页面地址隔开，每个参数用&隔开，例如：http://java.sun.com?name=liudong&mobile=123456，但是当使用POST方法时就会稍微有一点点麻烦。本小节的例子演示向如何查询手机号码所在的城市，代码如下： 
 
 
*    
 * Created on 2009-7-9 by skydong    
 */    
package http.demo;    
import java.io.IOException;    
import org.apache.commons.httpclient.*;    
import org.apache.commons.httpclient.methods.*;    
/** *//**   
 * 提交参数演示   
 * 该程序连接到一个用于查询手机号码所属地的页面   
 * 以便查询号码段1330227所在的省份以及城市   
 * @author skydong  
 */   
   
public class SimpleHttpClient ...{    
    public static void main(String[] args) throws IOException    
    ...{    
        HttpClient client = new HttpClient();    
        client.getHostConfiguration().setHost("www.imobile.com.cn", 80, "http");    
        HttpMethod method = getPostMethod();//使用POST方式提交数据    
     client.executeMethod(method);    
       //打印服务器返回的状态    
     System.out.println(method.getStatusLine());    
       //打印结果页面    
    String response =   new String(method.getResponseBodyAsString().getBytes("8859_1"));    
       //打印返回的信息    
     System.out.println(response);    
        method.releaseConnection();    
    }    
    /** *//**   
     * 使用GET方式提交数据   
   * @return   
     */   
    private static HttpMethod getGetMethod()...{    
        return new GetMethod("/simcard.php?simcard=1330227");    
    }    
    /** *//**   
     * 使用POST方式提交数据   
   * @return   
     */   
    private static HttpMethod getPostMethod()...{    
        PostMethod post = new PostMethod("/simcard.php");    
        NameValuePair simcard = new NameValuePair("simcard","1330227");    
        post.setRequestBody(new NameValuePair[] ...{ simcard});    
        return post;    
    }    
}    


在上面的例子中页面http://www.imobile.com.cn/simcard.php需要一个参数是simcard，这个参数值为手机号码段，即手机号码的前七位，服务器会返回提交的手机号码对应的省份、城市以及其他详细信息。GET的提交方法只需要在URL后加入参数信息，而POST则需要通过NameValuePair类来设置参数名称和它所对应的值 

3． 处理页面重定向 

在JSP/Servlet编程中response.sendRedirect方法就是使用HTTP协议中的重定向机制。它与JSP中的的区别在于后者是在服务器中实现页面的跳转，也就是说应用容器加载了所要跳转的页面的内容并返回给客户端；而前者是返回一个状态码，这些状态码的可能值见下表，然后客户端读取需要跳转到的页面的URL并重新加载新的页面。就是这样一个过程，所以我们编程的时候就要通过HttpMethod.getStatusCode()方法判断返回值是否为下表中的某个值来判断是否需要跳转。如果已经确认需要进行页面跳转了，那么可以通过读取HTTP头中的location属性来获取新的地址。 

状态码 
对应HttpServletResponse的常量 
详细描述 

301 
SC_MOVED_PERMANENTLY 
页面已经永久移到另外一个新地址 

302 
SC_MOVED_TEMPORARILY 
页面暂时移动到另外一个新的地址 

303 
SC_SEE_OTHER 
客户端请求的地址必须通过另外的URL来访问 

307 
SC_TEMPORARY_REDIRECT 
同SC_MOVED_TEMPORARILY 


下面的代码片段演示如何处理页面的重定向 
 
client.executeMethod(post);    
        System.out.println(post.getStatusLine().toString());     
        post.releaseConnection();    
        //检查是否重定向    
     int statuscode = post.getStatusCode();    
        if ((statuscode == HttpStatus.SC_MOVED_TEMPORARILY) ||    
            (statuscode == HttpStatus.SC_MOVED_PERMANENTLY) ||    
            (statuscode == HttpStatus.SC_SEE_OTHER) ||    
            (statuscode == HttpStatus.SC_TEMPORARY_REDIRECT))     
        ...{//读取新的URL地址    
         Header header = post.getResponseHeader("location");    
              if (header != null) ...{    
                 String newuri = header.getValue();    
                 if ((newuri == null) || (newuri.equals("")))    
                      newuri = "/";     
               GetMethod redirect = new GetMethod(newuri);    
               client.executeMethod(redirect);    
                System.out.println("Redirect:"+ redirect.getStatusLine().toString());     
                redirect.releaseConnection();    
            } else ...{    
               System.out.println("Invalid redirect");    
        } 


我们可以自行编写两个JSP页面，其中一个页面用response.sendRedirect方法重定向到另外一个页面用来测试上面的例子。 
本小节应该说是HTTP客户端编程中最常碰见的问题，很多网站的内容都只是对注册用户可见的，这种情况下就必须要求使用正确的用户名和口令登录成功后，方可浏览到想要的页面。因为HTTP协议是无状态的，也就是连接的有效期只限于当前请求，请求内容结束后连接就关闭了。在这种情况下为了保存用户的登录信息必须使用到Cookie机制。以JSP/Servlet为例，当浏览器请求一个JSP或者是Servlet的页面时，应用服务器会返回一个参数，名为jsessionid（因不同应用服务器而异），值是一个较长的唯一字符串的Cookie，这个字符串值也就是当前访问该站点的会话标识。浏览器在每访问该站点的其他页面时候都要带上jsessionid这样的Cookie信息，应用服务器根据读取这个会话标识来获取对应的会话信息。 

对于需要用户登录的网站，一般在用户登录成功后会将用户资料保存在服务器的会话中，这样当访问到其他的页面时候，应用服务器根据浏览器送上的Cookie中读取当前请求对应的会话标识以获得对应的会话信息，然后就可以判断用户资料是否存在于会话信息中，如果存在则允许访问页面，否则跳转到登录页面中要求用户输入帐号和口令进行登录。这就是一般使用JSP开发网站在处理用户登录的比较通用的方法。 

这样一来，对于HTTP的客户端来讲，如果要访问一个受保护的页面时就必须模拟浏览器所做的工作，首先就是请求登录页面，然后读取Cookie值；再次请求登录页面并加入登录页所需的每个参数；最后就是请求最终所需的页面。当然在除第一次请求外其他的请求都需要附带上Cookie信息以便服务器能判断当前请求是否已经通过验证。说了这么多，可是如果你使用httpclient的话，你甚至连一行代码都无需增加，你只需要先传递登录信息执行登录过程，然后直接访问想要的页面，跟访问一个普通的页面没有任何区别，因为类HttpClient已经帮你做了所有该做的事情了，太棒了！下面的例子实现了这样一个访问的过程 
 
*    
 * Created on 2009-7-9 by skydong 
 */    
package http.demo;    
import org.apache.commons.httpclient.*;    
import org.apache.commons.httpclient.cookie.*;    
import org.apache.commons.httpclient.methods.*;    
/** *//**   
 * 用来演示登录表单的示例   
 * @author skydong 
 */   
public class FormLoginDemo ...{    
    static final String LOGON_SITE = "localhost";    
    static final int    LOGON_PORT = 8080;    
    public static void main(String[] args) throws Exception...{    
        HttpClient client = new HttpClient();    
        client.getHostConfiguration().setHost(LOGON_SITE, LOGON_PORT);    
        //模拟登录页面login.jsp->main.jsp    
        PostMethod post = new PostMethod("/main.jsp");    
        NameValuePair name = new NameValuePair("name", "ld");         
        NameValuePair pass = new NameValuePair("password", "ld");         
        post.setRequestBody(new NameValuePair[]...{name,pass});    
       int status = client.executeMethod(post);    
       System.out.println(post.getResponseBodyAsString());    
       post.releaseConnection();      
       //查看cookie信息    
    CookieSpec cookiespec = CookiePolicy.getDefaultSpec();    
      Cookie[] cookies = cookiespec.match(LOGON_SITE, LOGON_PORT, "/", false, client.getState().getCookies());    
       if (cookies.length == 0) ...{    
           System.out.println("None");        
       } else ...{    
           for (int i = 0; i < cookies.length; i++) ...{    
              System.out.println(cookies[i].toString());        
           }    
      }    
       //访问所需的页面main2.jsp    
        GetMethod get = new GetMethod("/main2.jsp");    
        client.executeMethod(get);    
        System.out.println(get.getResponseBodyAsString());    
        get.releaseConnection();    
    }    
}    
 


5． 提交XML格式参数 

提交XML格式的参数很简单，仅仅是一个提交时候的ContentType问题，下面的例子演示从文件文件中读取XML信息并提交给服务器的过程，该过程可以用来测试Web服务。
 
import java.io.File;    
import java.io.FileInputStream;    
import org.apache.commons.httpclient.HttpClient;    
import org.apache.commons.httpclient.methods.EntityEnclosingMethod;    
import org.apache.commons.httpclient.methods.PostMethod;    
/** *//**   
 * 用来演示提交XML格式数据的例子   
 */   
public class PostXMLClient ...{    
    public static void main(String[] args) throws Exception ...{    
        File input = new File(“test.xml”);    
        PostMethod post = new PostMethod(“http://localhost:8080/httpclient/xml.jsp”);    
        // 设置请求的内容直接从文件中读取    
     post.setRequestBody(new FileInputStream(input));    
        if (input.length() < Integer.MAX_VALUE)     
           post.setRequestContentLength(input.length());    
        else               
           post.setRequestContentLength(EntityEnclosingMethod.CONTENT_LENGTH_CHUNKED);    
        // 指定请求内容的类型    
     post.setRequestHeader("Content-type", "text/xml; charset=GBK");    
        HttpClient httpclient = new HttpClient();     
        int result = httpclient.executeMethod(post);     
        System.out.println("Response status code: " + result);    
        System.out.println("Response body: ");    
        System.out.println(post.getResponseBodyAsString());    
        post.releaseConnection();    
    }    
}    


6． 通过HTTP上传文件 

httpclient使用了单独的一个HttpMethod子类来处理文件的上传，这个类就是MultipartPostMethod，该类已经封装了文件上传的细节，我们要做的仅仅是告诉它我们要上传文件的全路径即可，下面的代码片段演示如何使用这个类。 
 
MultipartPostMethod filePost = new MultipartPostMethod(targetURL);    
filePost.addParameter("fileName", targetFilePath);    
HttpClient client = new HttpClient();    
//由于要上传的文件可能比较大,因此在此设置最大的连接超时时间    
client.getHttpConnectionManager().getParams().setConnectionTimeout(5000);    
int status = client.executeMethod(filePost);    

上面代码中，targetFilePath即为要上传的文件所在的路径。 

7． 访问启用认证的页面 

我们经常会碰到这样的页面，当访问它的时候会弹出一个浏览器的对话框要求输入用户名和密码后方可，这种用户认证的方式不同于我们在前面介绍的基于表单的用户身份验证。这是HTTP的认证策略，httpclient支持三种认证方式包括：基本、摘要以及NTLM认证。其中基本认证最简单、通用但也最不安全；摘要认证是在HTTP 1.1中加入的认证方式，而NTLM则是微软公司定义的而不是通用的规范，最新版本的NTLM是比摘要认证还要安全的一种方式。 

下面例子是从httpclient的CVS服务器中下载的，它简单演示如何访问一个认证保护的页面： 
import org.apache.commons.httpclient.HttpClient;    
import org.apache.commons.httpclient.UsernamePasswordCredentials;    
import org.apache.commons.httpclient.methods.GetMethod;    
public class BasicAuthenticationExample ...{    
   public BasicAuthenticationExample() ...{    
    }    
   public static void main(String[] args) throws Exception ...{    
       HttpClient client = new HttpClient();    
        client.getState().setCredentials(    
            "www.verisign.com",    
            "realm",    
            new UsernamePasswordCredentials("username", "password")    
        );    
        GetMethod get = new GetMethod("https://www.verisign.com/products/index.html";);    
        get.setDoAuthentication( true );    
        int status = client.executeMethod( get );    
        System.out.println(status+""+ get.getResponseBodyAsString());    
        get.releaseConnection();    
    }    
}   



8． 多线程模式下使用httpclient 

多线程同时访问httpclient，例如同时从一个站点上下载多个文件。对于同一个HttpConnection同一个时间只能有一个线程访问，为了保证多线程工作环境下不产生冲突，httpclient使用了一个多线程连接管理器的类：MultiThreadedHttpConnectionManager，要使用这个类很简单，只需要在构造HttpClient实例的时候传入即可，代码如下： 
 
MultiThreadedHttpConnectionManager connectionManager =     
   new MultiThreadedHttpConnectionManager();    
HttpClient client = new HttpClient(connectionManager);   
