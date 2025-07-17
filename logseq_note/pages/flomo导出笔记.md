- flomo导出笔记
	- 参考
		- https://zhuanlan.zhihu.com/p/584861893
	- flomo笔记保存在浏览器的 IndexDB 中
		- 将代码粘贴到浏览器的控制台中执行
	- 导出代码
		- ```
		  async function exportIndexDB(dbname){
		    const loadScript = function(src){
		      return new Promise(function(resolve){ 
		        let script = document.createElement('script')
		        script.src = src;
		        script.onload = resolve;
		        document.body.appendChild(script);
		      })
		    } 
		    await loadScript('https://unpkg.com/dexie');
		    await loadScript('https://unpkg.com/dexie-export-import');
		    
		    let db = new Dexie(dbname);
		    const { verno, tables } = await db.open();
		    db.close();
		    
		    db = new Dexie(dbname);
		    db.version(verno).stores(tables.reduce((p,c) => {
		      p[c.name] = c.schema.primKey.keyPath || "";
		      return p;
		    }, {}));
		    return await db.export();
		  }
		  
		  function download(downfile,name) {
		    const tmpLink = document.createElement("a");
		    const objectUrl = URL.createObjectURL(downfile);
		    tmpLink.href = objectUrl;
		    tmpLink.download = name;
		    tmpLink.click();
		    URL.revokeObjectURL(objectUrl);
		  }
		  
		  let dbblob = await exportIndexDB('flomo');
		  download(dbblob,'flomo.json');
		  
		  ```