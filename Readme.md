# image-editor-cropper

[![Build Status](https://circleci.com/gh/image-editor-cropper/image-editor-cropper.svg?style=shield)](https://circleci.com/gh/image-editor-cropper/image-editor-cropper)
[![Package Version](https://img.shields.io/npm/v/image-editor-cropper.svg)](https://www.npmjs.com/package/image-editor-cropper)
[![Downloads](https://img.shields.io/npm/dm/image-editor-cropper.svg)](http://npm-stat.com/charts.html?package=image-editor-cropper&author=&from=&to=)

## Programmatic Usage

```shell
npm install image-editor-cropper --save
```

or

```shell
yarn add image-editor-cropper
```

### Synchronous

```javascript
const loadText = {
    "APPLY": "Apply",
    "BOLD": "Bold",
    "CANCEL": "Cancel",
    "CENTER": "Center",
    "CIRCLE": "Circle",
    "COLOR": "Color",
    "CROP": "Crop",
    "CUSTOM": "Custom",
    "DOWNLOAD": "Download",
    "DELETE": "Delete",
    "DELETE_ALL": "DeleteAll",
    "DRAW": "Draw",
    "FILL": "Fill",
    "FREE": "Free",
    "HAND": "Hand",
    "HISTORY": "History",
    "ITALIC": "Italic",
    "LEFT": "Left",
    "LOAD": "Load",
    "RANGE": "Range",
    "RECTANGLE": "Rectangle",
    "REDO": "Redo",
    "RESET": "Reset",
    "RIGHT": "Right",
    "SHAPE": "Shape",
    "SQUARE": "Square",
    "STRAIGHT": "Straight",
    "STROKE": "Stroke",
    "TRIANGLE": "Triangle",
    "TEXT": "Text",
    "TEXT_SIZE": "Text size",
    "UNDERLINE": "Underline",
    "UNDO": "Undo",
    "ZOOM_IN": "ZoomIn",
    "ZOOM_OUT": "ZoomOut"
}
```
### React.js / Next.js Example

```javascript
import React from 'react';
import ImageEditorCropper from 'image-editor-cropper';
import styled from "styled-components";
import colors from "@constants/Colors";
import {crossIcon} from "@helpers/Icons"; // SVG icons
const ImageModalWrap = styled.div`
    position:fixed;top:0;bottom:0;left:0;right:0;z-index:9999;display:flex;justify-content:center;align-items:center;background:#0000002b;
    & .back{position:absolute;left:0;top:0;right:0;bottom:0;}
    & .inner{
        background:${colors.white};z-index:99999;max-width:800px;width:calc(100% - 40px);height:calc(100vh - 20px);position:relative;border-radius:17px;overflow:hidden;
        & .header{
            display:flex;align-items:center;column-gap:10px;justify-content:space-between;padding:10px 20px;background:${colors.black};border-radius:17px 17px 0 0;
            & span{font-size:20px;line-height:24px;font-weight:600;color:${colors.white};}
            & .crop{
                display:flex;align-items:center;justify-content:center;padding:8px 15px;border-radius:6px;border:none;background:${colors.primaryButton};color:${colors.white};font-size:14px;line-height:1;cursor:pointer;margin-left:auto;
            }
            & .close{
                z-index:1;width:25px;height:25px;display:flex;align-items:center;justify-content:center;border:none;border-radius:20px;background:${colors.secondaryButton};cursor:pointer;
                & svg{fill:${colors.black};}
            }
        }
        & .body{padding:0;display:flex;flex-direction:column;flex:1;overflow-y:auto;height:calc(100% - 50px);}
    }
    @media (max-width:479px){
        & .inner{
            & .header{
                & span{font-size:16px;}
            }
        }
    }
`;
export default class ImageEditor extends React.Component
{
    rootEl = React.createRef();
    imageEditorInst = null;
    componentDidMount(){
        let boxWidth = window.outerWidth;
        let boxHeight = window.outerHeight;
        this.imageEditorInst = new ImageEditorCropper(this.rootEl.current,{
            includeUI: {
                locale: this.localFactory(),
                loadImage: {
                    path: this.props.image,
                    name: this.props.avatar.name
                },
                // menu: ["crop","flip","rotate","draw","shape","text"],
                menu: ["crop","draw","shape","text"],
                initMenu: '',
                uiSize: {width: '100%',height: '100%'},
                menuBarPosition: 'top'
            },
            cssMaxHeight: ((boxHeight > 680) ? 350 : 300),
            cssMaxWidth: ((boxWidth > 600) ? 550 : 320),
            selectionStyle: {cornerSize: 20,rotatingPointOffset: 70},
            usageStatistics: false
        });
    }
    componentWillUnmount(){
        this.imageEditorInst.destroy();
        this.imageEditorInst = null;
    }
    shouldComponentUpdate(nextProps){
        return false;
    }
    getInstance(){
        return this.imageEditorInst;
    }
    getRootElement(){
        return this.rootEl.current;
    }
    localFactory = () => {
        return {
            Apply: loadText.APPLY,
            Bold: loadText.BOLD,
            Cancel: loadText.CANCEL,
            Center: loadText.CENTER,
            Circle: loadText.CIRCLE,
            Color: loadText.COLOR,
            Crop: loadText.CROP,
            Custom: loadText.CUSTOM,
            Download: loadText.DOWNLOAD,
            Delete: loadText.DELETE,
            DeleteAll: loadText.DELETE_ALL,
            Draw: loadText.DRAW,
            Fill: loadText.FILL,
            Free: loadText.FREE,
            Hand: loadText.HAND,
            History: loadText.HISTORY,
            Italic: loadText.ITALIC,
            Left: loadText.LEFT,
            Load: loadText.LOAD,
            Range: loadText.RANGE,
            Rectangle: loadText.RECTANGLE,
            Redo: loadText.REDO,
            Reset: loadText.RESET,
            Right: loadText.RIGHT,
            Shape: loadText.SHAPE,
            Square: loadText.SQUARE,
            Straight: loadText.STRAIGHT,
            Stroke: loadText.STROKE,
            Triangle: loadText.TRIANGLE,
            Text: loadText.TEXT,
            "Text size": loadText.TEXT_SIZE,
            Underline: loadText.UNDERLINE,
            Undo: loadText.UNDO,
            ZoomIn: loadText.ZOOM_IN,
            ZoomOut: loadText.ZOOM_OUT
        }
    }
    handleClose = () => {
        this.props.changeHandler({type: "closed"});
    }
    handleCropImage = async() => {
        const modifiedImage = await this.getImageFromBinary(this.imageEditorInst.toDataURL({format: this.props.avatar.type.split("/")[1]}),this.props.avatar.name);
        this.props.changeHandler({type: "set",avatar: modifiedImage});
    }
    getImageFromBinary = async(imageSrc,fileName) => {
        if(!imageSrc){
            return;
        }
        var arr = imageSrc.split(",");
        var mime = arr[0].match(/:(.*?);/)[1];
        var bstr = Buffer.from(arr[1],'base64').toString('binary');
        var n = bstr.length;
        var u8arr = new Uint8Array(n);
        while(n--){
            u8arr[n] = bstr.charCodeAt(n);
        }
        return new File([u8arr],fileName,{type: mime});
    }
    render(){
        return (
            <ImageModalWrap>
                <div className="back"></div>
                <div className="inner">
                    <div className="header">
                        <span>IMAGE EDITOR</span>
                        <button className="crop" onClick={this.handleCropImage} type="button">SAVE</button>
                        <button onClick={this.handleClose} title="CLOSE" className="close">{crossIcon({width:24,height:24})}</button>
                    </div>
                    <div className="body">
                        <div ref={this.rootEl}/>
                    </div>
                </div>
            </ImageModalWrap>
        );
    }
}
```