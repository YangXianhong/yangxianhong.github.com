---
layout: post
title: "java下载中转实现方法"
category: java
tags: 
 - java 
 - download
---

      最近项目上做了一个移动办公项目，开始客户预订是在内网使用。做完后，客户又想可以连接外网使用该平台。麻烦就出现了，本来将该系统服务端发布到外网上就可以了，但在实际评估中发现客户端很多的下载、新闻、图片展示等功能都是使用的其它应用系统上的链接。而这些系统都是在内网中，无法在外网中使用。如果要改造使之可以正常下载展示这些内容，改动量就会很大。经过考虑，我想最好的办法，是将这些链接的内容在服务端（服务端是内外网都可以访问的）做转换，使它可以直接下载内网中其它系统的文件。原理是java下载时将下载服务器端文件改为下载网络文件。听起来是有些拗口，那就让我把实现后的原代码贴出来吧
##下载实现
web.xml中servlet配置
<pre>
  <!-- 下载servlet start-->
   <servlet>
    <servlet-name>DownloadFile</servlet-name>
    <servlet-class>com.gygs.cportalsend.servlet.DownloadServlet</servlet-class>
    <load-on-startup>0</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>DownloadFile</servlet-name>
    <url-pattern>/downloadFile</url-pattern>
  </servlet-mapping>  
  <!-- 下载servlet end-->
</pre>

##servlet java类
<pre>
import java.io.BufferedOutputStream;
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLDecoder;
import java.util.Random;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 初始化
 * @author ziwen
 */
public class DownloadServlet extends HttpServlet {
	
	//http://localhost:8080/gygsmobile/downloadFile?fileUrl=http://g.hiphotos.baidu.com/image/pic/item/e7cd7b899e510fb3d9b14f9ada33c895d1430c16.jpg
/*	protected void doGet(HttpServletRequest request,HttpServletResponse response)throws ServletException{
		  System.out.println("调用doGet方法");
		// 下载网络文件  
	        int bytesum = 0;  
	        int byteread = 0;  
	        try {  
//	        URL url = new URL("http://g.hiphotos.baidu.com/image/pic/item/e7cd7b899e510fb3d9b14f9ada33c895d1430c16.jpg");  
//	  
//	    
//	       URLConnection conn = url.openConnection();  
//	       InputStream fis = conn.getInputStream();  
	        	String fileUrl=request.getParameter("fileUrl");
	        //	fileUrl=URLDecoder.decode(fileUrl, "UTF-8");
	        byte[] buffer = getUrlFileData(fileUrl);
//	        fis.read(buffer);  
//	        fis.close();  
	        // 清空response  
	        response.reset();  
	        // 设置response的Header  
//	        response.addHeader("Content-Disposition", "attachment;filename="  
//	                + new String("文件asd"));  
//	        response.addHeader("Content-Length", "");  
	        OutputStream toClient = new BufferedOutputStream(  
	                response.getOutputStream());  
//	        response.setContentType("application/octet-stream");  
	        toClient.write(buffer);  
	        toClient.flush();  
	        toClient.close();  
	        } catch (Exception e) {  
	           e.printStackTrace();  
	        }
		 }
		 */
	
	//http://localhost:8080/gygsmobile/downloadFile?fileUrl=http://g.hiphotos.baidu.com/image/pic/item/e7cd7b899e510fb3d9b14f9ada33c895d1430c16.jpg
	protected void doGet(HttpServletRequest request,HttpServletResponse response)throws ServletException{
		  System.out.println("调用doGet方法");
		// 下载网络文件  
	        int bytesum = 0;  
	        int byteread = 0;  
	        try {  
//	        URL url = new URL("http://g.hiphotos.baidu.com/image/pic/item/e7cd7b899e510fb3d9b14f9ada33c895d1430c16.jpg");  
//	  
//	    
//	       URLConnection conn = url.openConnection();  
//	       InputStream fis = conn.getInputStream();  
	        	String fileUrl=request.getParameter("fileUrl");
	        	String fileUrlName=request.getParameter("fileUrlName");
	        	System.out.println("URLURLURLURLURLfileUrl:"+fileUrl);
	        	System.out.println("URLURLURLURLURLfileUrlName:"+fileUrlName);
	        	fileUrl=URLDecoder.decode(fileUrl, "UTF-8");
//	        	System.out.println("URLURLURLURLURL2:"+fileUrl);
	        	if(fileUrlName==null || "".equals(fileUrlName)){
	        		fileUrlName=getRandomString(8);
	        		String ext = fileUrl.substring(fileUrl.lastIndexOf(".") + 1); 
	        		fileUrlName=fileUrlName+"."+ext;
	        		  //              .toUpperCase(); 
	        	}
	        byte[] buffer = getUrlFileData(fileUrl);
//	        fis.read(buffer);  
//	        fis.close();  
	        // 清空response  
	        response.reset();  
	        // 设置response的Header  
	        response.addHeader("Content-Disposition", "attachment;filename="  
	                + new String(fileUrlName));  
//	        response.addHeader("Content-Length", "");  
	        OutputStream toClient = new BufferedOutputStream(  
	                response.getOutputStream());  
	        response.setContentType("application/octet-stream");  
	        toClient.write(buffer);  
	        toClient.flush();  
	        toClient.close();  
	        } catch (Exception e) {  
	           e.printStackTrace();  
	        }
		 }
	

		protected void doPost(HttpServletRequest request,HttpServletResponse response)throws ServletException{
		 System.out.println("调用doPost方法");
		 doGet(request,response);
		 }
		
		
		 //获取链接地址文件的byte数据  
		    public static byte[] getUrlFileData(String fileUrl) throws Exception  
		    {  
		        URL url = new URL(fileUrl);  
		        HttpURLConnection httpConn = (HttpURLConnection) url.openConnection();  
		        httpConn.connect();  
		       InputStream cin = httpConn.getInputStream();  
		        ByteArrayOutputStream outStream = new ByteArrayOutputStream();  
		        byte[] buffer = new byte[1024];  
		        int len = 0;  
		        while ((len = cin.read(buffer)) != -1) {  
		           outStream.write(buffer, 0, len);  
		        }  
		       cin.close();  
		       byte[] fileData = outStream.toByteArray();  
		       outStream.close();  
		       return fileData;  
		   }  

		    
		    public String getRandomString(int length) { 
		        StringBuffer buffer = new StringBuffer("0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"); 
		        StringBuffer sb = new StringBuffer(); 
		        Random r = new Random(); 
		        int range = buffer.length(); 
		        for (int i = 0; i < length; i ++) { 
		            sb.append(buffer.charAt(r.nextInt(range))); 
		        } 
		        return sb.toString(); 
		    }

}

</pre>

##图片展示
<pre>
package com.isoftstone.service;

import java.io.UnsupportedEncodingException;
import java.util.List;

import javax.sql.DataSource;

import oracle.sql.CLOB;

import com.gygs.cportalsend.common.DataSourceGetor;
import com.gygs.cportalsend.common.Resource;
import com.isoftstone.service.pojo.InformationBean;
import com.isoftstone.service.pojo.InformationContent;
import com.jfinal.plugin.activerecord.Db;
import com.jfinal.plugin.activerecord.Record;

public class NewShowService {
	
	public InformationBean newDetail(String new_id){
		StringBuffer sql=new StringBuffer();
		//获取新闻信息sql
		sql.append("select  title,sub_title,author_name,modify_date,view_num,promulgation_charge  from portal_information i where i.id="+new_id);
		
		 DataSource dataSource=null;
	     dataSource=DataSourceGetor.getDataSourceGetorInstence().getDataSource("BGMH");
	     
	     InformationBean informationBean=new InformationBean();
	    	List<Object[]> list=Db.query(dataSource,sql.toString());
	    	if(list!=null && list.size()>0){
	    		Object[] res=list.get(0);
	    		informationBean.setTitle(res[0].toString());
	    		informationBean.setSubTitle(res[1]==null?"":res[1].toString());
	    		informationBean.setAuthorName(res[2]==null?"":res[2].toString());
	    		informationBean.setModifyDateCn(res[3]==null?"":res[3].toString());
	    		informationBean.setViewNum(Integer.parseInt(res[4]==null?"":res[4].toString()));
	    		informationBean.setPromulgationCharge(res[5]==null?"":res[5].toString());
	    	}
	    	
	    	informationBean.setContent(findInformationContentByInformationId(Integer.parseInt(new_id)).getContent());
	     
		
		 return informationBean;
	}

	//http://localhost:8080/gygsmobile/newsDetail_mobile.jsp?NEWID=4581
	  public InformationContent findInformationContentByInformationId(int informationId) {

	        // TODO Auto-generated method stub
	        StringBuffer sql=new StringBuffer();
			//查询新闻内容：CLOB字段
			sql.append("select content from portal_information_content i where i.information_id="+informationId);
	        
			 DataSource dataSource=null;
		     dataSource=DataSourceGetor.getDataSourceGetorInstence().getDataSource("BGMH");
		     
		     InformationContent informationContent=new InformationContent();
		     List<Record> list=Db.find(dataSource,sql.toString());
		    	if(list!=null && list.size()>0){
		    		String re=list.get(0).getStr("CONTENT");
		    		try {
		    			String newStr2="";
						//将新闻内容中的<img>标签中的url路径地址，替换成新的下载地址，即download_url?urlFile=old_url
		    			newStr2=re.replace("/gyygbg/", Resource.HOST_URL+"/gygsmobile/downloadFile?fileUrl="+Resource.YGBG_URL+"/gyygbg/");	
						informationContent.setContent(newStr2);
					} catch (Exception e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
		    	}
		     
	        return informationContent;
	    }
}

</pre>

