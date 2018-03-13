# history_fabric.js
类似于浏览器的前进后退功能

## 我用 fabric.js 做了什么
以下是已经完成的或者正在实现中的

克隆  删除  剪切 变形  历史记录 滤镜/整体滤镜 翻转 锁定 rgb 画布辅助(透视，正透视，成角透视) 组合、打散 图层管理 图片可见/不可见 等

## 基于vue框架怎么改
我现在的项目是用vue开发的

初始化数据是 _config 命名不合法，也不报错 改为 config 
在初始化 this.$refs.undo 获取不到 在mounted 中获取

```
    //在data
    config : {
            canvasState             : [],
            currentStateIndex       : -1,
            redoStatus              : false,
            undoStatus              : false,
            undoFinishedStatus      : 1,
            redoFinishedStatus      : 1,
            undoButton              : this.$refs.undo,
            redoButton              : this.$refs.redo,
        }
    // 在mounted
    this.config.redoButton = this.$refs.redo;

    //画布的事件
    canvasDataChange(){
      let _self = this;
      this.canvas.on('object:modified', function(){
          _self.updateCanvasState();
      });
      this.canvas.on('object:added', function(){
          _self.updateCanvasState()
      });
      this.canvas.on('object:removed', function(){
          _self.updateCanvasState()
      });
      this.canvas.on('object:rotating', function(){
          _self.updateCanvasState()
      });
    },
    updateCanvasState() {
        var _self = this;
		if(this.config.undoStatus == false && this.config.redoStatus == false){
			var jsonData  = _self.canvas.toJSON();
			var canvasAsJson        = JSON.stringify(jsonData);
			if(_self.config.currentStateIndex < _self.config.canvasState.length-1){
				var indexToBeInserted                  = _self.config.currentStateIndex+1;
				_self.config.canvasState[indexToBeInserted] = canvasAsJson;
				var numberOfElementsToRetain           = indexToBeInserted+1;
				_self.config.canvasState                    = _self.config.canvasState.splice(0,numberOfElementsToRetain);
			}else{
	    	    _self.config.canvasState.push(canvasAsJson);
			}
            _self.config.currentStateIndex = _self.config.canvasState.length-1;
            if((_self.config.currentStateIndex == _self.config.canvasState.length-1) && _self.config.currentStateIndex != -1){
                _self.config.redoButton.disabled= true
            }
		}
    }, 
    undo() {
        let _self =this;
		if(this.config.undoFinishedStatus){
			if(this.config.currentStateIndex == -1){
	    	    this.config.undoStatus = false;
			}
			else{
		        if (this.config.canvasState.length >= 1) {
        	        this.config.undoFinishedStatus = 0;
                    if(this.config.currentStateIndex != 0){
                        this.config.undoStatus = true;
                        this.canvas.loadFromJSON(this.config.canvasState[this.config.currentStateIndex-1],function(){
                                var jsonData = JSON.parse(_self.config.canvasState[_self.config.currentStateIndex-1]);
                                _self.canvas.renderAll();
                                _self.config.undoStatus = false;
                                _self.config.currentStateIndex -= 1;
                                    // _self.config.undoButton.removeAttribute("disabled");
                                    _self.config.undoButton.disabled = false;
                                    if(_self.config.currentStateIndex !== _self.config.canvasState.length-1){
                                        // _self.config.redoButton.removeAttribute('disabled');
                                        _self.config.redoButton.disabled = false;
                                    }
                                _self.config.undoFinishedStatus = 1;
                        });
                    }
                    else if(_self.config.currentStateIndex == 0){
                        _self.canvas.clear();
                        _self.config.undoFinishedStatus = 1;
                        _self.config.undoButton.disabled= "disabled";
                        // _self.config.redoButton.removeAttribute('disabled');
                        _self.config.redoButton.disabled = false;
                        _self.config.currentStateIndex -= 1;
                    }
                }
			}
		}
    },
    redo() {
        let _self = this;
		if(this.config.redoFinishedStatus){
			if((this.config.currentStateIndex == this.config.canvasState.length-1) && this.config.currentStateIndex != -1){
				this.config.redoButton.disabled= true;
			}else{
		  	if(this.config.canvasState.length > this.config.currentStateIndex && this.config.canvasState.length != 0){
                this.config.redoFinishedStatus = 0;
                this.config.redoStatus = true;
                this.canvas.loadFromJSON(this.config.canvasState[this.config.currentStateIndex+1],function(){
                    var jsonData = JSON.parse(_self.config.canvasState[_self.config.currentStateIndex+1]);
                    _self.canvas.renderAll();
                    _self.config.redoStatus = false;
                    _self.config.currentStateIndex += 1;
                    if(_self.config.currentStateIndex != -1){
                       _self.config.redoButton.disabled = false;
                    }
                    _self.config.redoFinishedStatus = 1;
                    if((_self.config.currentStateIndex == _self.config.canvasState.length-1) && _self.config.currentStateIndex != -1){
                        _self.config.redoButton.disabled= true;
                    }
		      });
		    }
			}
		}
	},

```

### 接下来做什么

继续根据产品的绣球，用fabric.js做开发，也会在我的github上做分享
