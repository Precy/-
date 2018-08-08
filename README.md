# -
不用插件实现文件上传
实现多文件不限格式上传，同时带上传进度
html:--------------------------------------------------------------------------------------------
<div class="tab-pane" id="visualize">
	                   		<div id="visualizeBox">
	                   			<button class="btn btn-success visualize-button btn-xs" type="button"><i class="fa fa-plus"></i>选择文件</button>
								<input type="file" class="fileMutiply" name="files" multiple="multiple" style="display:none">
								<ul class="list-unstyled m-t-20 m-l-25 visualize-file">
									<#if sampleFilesList??>
										<#list sampleFilesList as sampleFiles>
											<#if sampleFiles.type=='visualize'>
												<li class=m-b-5 id="file${sampleFiles_index}">
								        			<a href="${sampleFiles.fileLink}" id="nameLink${sampleFiles_index}" class="progressName" >${sampleFiles.fileName}</a>&nbsp;&nbsp;		
								        			<span class="progressNum">${sampleFiles.fileSize}</span>&nbsp;&nbsp;&nbsp;&nbsp;	
								        			<span class="delete-other" ><i class="fa fa-times"></i></span>
							        			</li>
							        		</#if>	
										</#list>
									</#if>
								</ul>
							</div>
					   </div>
					   <h4 class="base-info">认证</h4>
					   <div class="tab-pane" id="identification">
	                   		<div id="identificationBox">
	                   			<button class="btn btn-success identification-button btn-xs" type="button"><i class="fa fa-plus"></i>选择文件</button>
								<!-- <input type="file" class="fileMutiply" name="files" multiple="multiple" style="display:none"> -->
								<ul class="list-unstyled m-t-20 m-l-25 identification-file">
									<#if sampleFilesList??>
										<#list sampleFilesList as sampleFiles>
											<#if sampleFiles.type=='identification'>
												<li class=m-b-5 id="file${sampleFiles.item_index}"> 
								        			<a href="${sampleFiles.fileLink}" id="nameLink${sampleFiles.item_index}" class="progressName" >${sampleFiles.fileName}</a>&nbsp;&nbsp;		
								        			<span class="progressNum">${sampleFiles.fileSize}</span>&nbsp;&nbsp;&nbsp;&nbsp;	
								        			<span class="delete-other" ><i class="fa fa-times"></i></span>
							        			</li>
							        		</#if>	
										</#list>
									</#if>
								</ul>
							</div>
	                  </div>



js:--------------------------------------------------------------------------------------------
  /**
   * 上传文件
   */
  var i=0;
  var j=0;
  var type;
  $(function(){
	  $('.visualize-button').click(function() {
		  $(".fileMutiply").click();
		  type = 'visualize';
	  })
	  $('.identification-button').click(function() {
		  $(".fileMutiply").click();
		  type = 'identification';
	  })
	  
      $(".fileMutiply").change(function eventStart(){
    	 var liLength;
         var currentFile =this.files; //获取当前选择的文件对象
         if(type == 'visualize') {
        	liLength = $(".visualize-file").find('li').length;
         }else if(type == 'identification') {
        	 liLength = $(".identification-file").find('li').length;
         }
         for(var m=0;m<currentFile.length;m++){  //循环添加进度条
             efileName = currentFile[m].name ; 
             if(currentFile[m].size> 1024 * 10240) {
            	 layer.alert('请勿上传超过10M的文件！');
            	 $(".fileMutiply").val("");
            	 return false;
             }
             if (currentFile[m].size> 1024 * 1024){
            	 sfileSize = (Math.round(currentFile[m].size /(1024 * 1024))).toString() + 'MB';
             }
             else{
            	 sfileSize = (Math.round(currentFile[m].size/1024)).toString() + 'KB';
             }
	         if(type == 'visualize') {
	        	  var fileNameBox = [];
	        	  $('.visualize-file li').each(function(i, v) {
	        		  var fileName = $(v).find('.progressName').text().trim();
	        		  fileNameBox.push(fileName);
	        	  })
	        	  if(fileNameBox.indexOf(efileName) > -1) {
	        		  layer.alert('请勿重复上传附件！');
	        		  $(".fileMutiply").val("");
	        		  return false;
	        	  }
	        	  
	        	  $(".visualize-file").append(
	        			  "<li class=m-b-5  id=file"+ (m+liLength)+">" +
	        			  "<a href='' id=nameLink"+(m+liLength)+" class='progressName' >"+efileName+"</a>&nbsp;&nbsp;" +		
	        			  "<span class='progressNum'>"+(sfileSize)+"</span>&nbsp;&nbsp;&nbsp;&nbsp;" +		
	        			  "<span class='delete-other' ><i class='fa fa-times'></i></span>"+
	        			  "<div class='progress progress-striped active'>" +
	        			  "<div class='progress-bar progress-bar-success'>Success</div>" +
	        			  "</div>" +
		          	"</li>");
	         }else if(type == 'identification') {
	        	 var fileNameBox = [];
	        	  $('.identification-file li').each(function(i, v) {
	        		  var fileName = $(v).find('.progressName').text().trim();
	        		  fileNameBox.push(fileName);
	        	  })
	        	  if(fileNameBox.indexOf(efileName) > -1) {
	        		  layer.alert('请勿重复上传附件！');
	        		  $(".fileMutiply").val("");
	        		  return false;
	        	  }
	        	 
	        	  $(".identification-file").append(
	        			  "<li class=m-b-5  id=file"+ (m+liLength)+">" +
	        			  "<a href='' id=nameLink"+(m+liLength)+" class='progressName' >"+efileName+"</a>&nbsp;&nbsp;" +		
	        			  "<span class='progressNum'>"+(sfileSize)+"</span>&nbsp;&nbsp;&nbsp;&nbsp;" +		
	        			  "<span class='delete-other' ><i class='fa fa-times'></i></span>"+
	        			  "<div class='progress progress-striped active'>" +
	        			  "<div class='progress-bar progress-bar-success'>Success</div>" +
	        			  "</div>" +
		          	"</li>");
	         }  
          }
          sendAjax();
          function sendAjax() {  
            if(j>=currentFile.length) {  //采用递归的方式循环发送ajax请求
               $(".fileMutiply").val("");
               j=0;
               return;  
            }
           var formData = new FormData();
           formData.append('files', currentFile[j]);  //将该file对象添加到formData对象中
                $.ajax({
                    url:'/FileManager/uploadOtherFiles',
                    type:'POST',
                    cache: false,
                    data:{},//需要什么参数，自己配置
                    data: formData,//文件以formData形式传入
                    processData : false, 
                   //必须false才会自动加上正确的Content-Type 
                   contentType : false , 
                   //beforeSend:beforeSend,//发送请求
                   //complete:complete,//请求完成          
                   xhr: function(){      //监听用于上传显示进度
                        var xhr = $.ajaxSettings.xhr();
                        //debugger
                        if(onprogress && xhr.upload) {
                         xhr.upload.addEventListener("progress" , function(evt) {
                        	   var loaded = evt.loaded;     //已经上传大小情况 
	                  	       var tot = evt.total;      //附件总大小 
	                  	       var per = Math.floor(100*loaded/tot);  //已经上传的百分比 
	                  	       if(type == 'visualize') {
	                  	    	   $('.visualize-file').find("#file"+(j+liLength)).find(".progress-bar-success").text(per +"%");
                         		   $('.visualize-file').find("#file"+(j+liLength)).find(".progress-bar-success").css('width', per+'%');
	                          }else if(type == 'identification') {
	                        	  $('.identification-file').find("#file"+(j+liLength)).find(".progress-bar-success").text(per +"%");
	  							  $('.identification-file').find("#file"+(j+liLength)).find(".progress-bar-success").css('width', per+'%');
	                          }
	                  	       
                         }, false);
                         return xhr;
                        }
                       } ,
                    success:function(data){
                    	var data = JSON.parse(data);
                   	 	console.log(data);
	                   	if(type == 'visualize') {
	            	    	   $('.visualize-file').find("#file"+(j+liLength)).find(".progress-striped").remove();//移除进度条样式
	            	    	   $('.visualize-file').find("#nameLink"+(j+liLength)).attr('href', '/sample/sampleFileDownload?path='+data.path+'&filename='+efileName);
	                    }else if(type == 'identification') {
	                  	  	  $('.identification-file').find("#file"+(j+liLength)).find(".progress-striped").remove();//移除进度条样式
							  $('.identification-file').find("#nameLink"+(j+liLength)).attr('href', '/sample/sampleFileDownload?path='+data.path+'&filename='+efileName);
	                    }
                        j++; //递归条件
                        sendAjax();
                    },
                    error:function(xhr){
                      layer.alert("上传出错!");
                      if(type == 'visualize') {
	           	    	   $('.visualize-file').find("#file"+(j+liLength)).closest('li').remove();//移除上传
	                   }else if(type == 'identification') {
	                	   $('.identification-file').find("#file"+(j+liLength)).closest('li').remove();//移除上传
	                   }
                    }                              
                });
            
          
            } 
     })
     
    function onprogress(evt){
	       var loaded = evt.loaded;     //已经上传大小情况 
	       var tot = evt.total;      //附件总大小 
	       var per = Math.floor(100*loaded/tot);  //已经上传的百分比 
	       $((j+liLength) +"file").find(".progress-bar-success").text(per +"%");
	       $((j+liLength) +"file").find(".progress-bar-success").css('width', per+'%');
      };
 })
 $('.delete-other').live('click', function() {
	 $(this).closest('li').remove();
 })
 
 
 
