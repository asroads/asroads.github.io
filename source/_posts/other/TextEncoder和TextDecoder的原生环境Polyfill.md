---
title: TextEncoder和TextDecoder的原生环境Polyfill
comments: true
categories: other
tags:
  - js
abbrlink: 30ffd5aa
date: 2021-12-08 19:21:36
---

项目开发的通信模块用到了[TextEncoder](https://developer.mozilla.org/zh-CN/docs/Web/API/TextEncoder)和[TextDecoder](https://developer.mozilla.org/zh-CN/docs/Web/API/TextDecoder)但是在原生环境ios出包的时候报错了`typeof TextEncoder === "undefined"`错误，应该环境没有这两个类的实现，于是想到了要寻找一个Polyfill。下面简单的介绍一下。
<!--more-->
关于这两个类的Polyfill 还是蛮多的，自己搜索得到一个性能和功能都不错的类库。[FastestSmallestTextEncoderDecoder](https://github.com/anonyco/FastestSmallestTextEncoderDecoder)

下面简单的介绍一下：

### 环境

类库地址：https://github.com/anonyco/FastestSmallestTextEncoderDecoder

把下面的源码直接放到项目内，运行时加载即可。

### 代码源码

[FastestTextDecoderPolyfill.src.js](https://github.com/anonyco/FastestSmallestTextEncoderDecoder/blob/master/individual/FastestTextDecoderPolyfill.src.js)

```javascript
(function(window){
  "use strict";
  var log = Math.log;
  var LN2 = Math.LN2;
  var clz32 = Math.clz32 || function(x) {return 31 - log(x >>> 0) / LN2 | 0};
  var fromCharCode = String.fromCharCode;
  var Object_prototype_toString = ({}).toString;
  var NativeSharedArrayBuffer = window["SharedArrayBuffer"];
  var sharedArrayBufferString = NativeSharedArrayBuffer ? Object_prototype_toString.call(NativeSharedArrayBuffer) : "";
  var NativeUint8Array = window.Uint8Array;
  var patchedU8Array = NativeUint8Array || Array;
  var arrayBufferString = Object_prototype_toString.call((NativeUint8Array ? ArrayBuffer : patchedU8Array).prototype);
  function decoderReplacer(encoded){
	var cp0 = encoded.charCodeAt(0), codePoint=0x110000, i=0, stringLen=encoded.length|0, result="";
    switch(cp0 >>> 4) {
    	// no 1 byte sequences
		case 12:
		case 13:
			codePoint = ((cp0 & 0x1F) << 6) | (encoded.charCodeAt(1) & 0x3F);
			i = codePoint < 0x80 ? 0 : 2;
			break;
		case 14:
			codePoint = ((cp0 & 0x0F) << 12) | ((encoded.charCodeAt(1) & 0x3F) << 6) | (encoded.charCodeAt(2) & 0x3F);
			i = codePoint < 0x800 ? 0 : 3;
			break;
		case 15:
			if ((cp0 >>> 3) === 30) {
				codePoint = ((cp0 & 0x07) << 18) | ((encoded.charCodeAt(1) & 0x3F) << 12) | ((encoded.charCodeAt(2) & 0x3F) << 6) | encoded.charCodeAt(3);
				i = codePoint < 0x10000 ? 0 : 4;
			}
    }
    if (i) {
        if (stringLen < i) {
        	i = 0;
        } else if (codePoint < 0x10000) { // BMP code point
			result = fromCharCode(codePoint);
		} else if (codePoint < 0x110000) {
			codePoint = codePoint - 0x10080|0;//- 0x10000|0;
			result = fromCharCode(
				(codePoint >>> 10) + 0xD800|0,  // highSurrogate
				(codePoint & 0x3ff) + 0xDC00|0 // lowSurrogate
			);
		} else i = 0; // to fill it in with INVALIDs
	}
	
	for (; i < stringLen; i=i+1|0) result += "\ufffd"; // fill rest with replacement character
	
	return result;
  }
  function TextDecoder(_, opts){/*this["ignoreBOM"] = !!opts && !!opts["ignoreBOM"]*/};
  TextDecoder["prototype"]["decode"] = function(inputArrayOrBuffer){
    var buffer = (inputArrayOrBuffer && inputArrayOrBuffer.buffer) || inputArrayOrBuffer;
    var asObjectString = Object_prototype_toString.call(buffer);
    if (asObjectString !== arrayBufferString && asObjectString !== sharedArrayBufferString && inputArrayOrBuffer !== undefined)
      throw TypeError("Failed to execute 'decode' on 'TextDecoder': The provided value is not of type '(ArrayBuffer or ArrayBufferView)'");
    var inputAs8 = NativeUint8Array ? new patchedU8Array(buffer) : buffer;
    var resultingString = "";
    for (var index=0/*inputAs8[0]!==0xEF||inputAs8[1]!==0xBB||inputAs8[2]!==0xBF||this["ignoreBOM"]?0:3*/,len=inputAs8.length|0; index<len; index=index+32768|0)
      resultingString += fromCharCode.apply(0, inputAs8[NativeUint8Array ? "subarray" : "slice"](index,index+32768|0));

    return resultingString.replace(/[\xc0-\xff][\x80-\xbf]+|[\x80-\xff]/g, decoderReplacer);
  }
  if (!window["TextDecoder"]) window["TextDecoder"] = TextDecoder;
})(typeof global == "" + void 0 ? typeof self == "" + void 0 ? this : self : global);
```

[FastestTextEncoderPolyfill.src.js](https://github.com/anonyco/FastestSmallestTextEncoderDecoder/blob/master/individual/FastestTextEncoderPolyfill.src.js)

```javascript
/** @define {boolean} */
var ENCODEINTO_BUILD = true;

(function(window){
	"use strict";
	var log = Math.log;
	var LN2 = Math.LN2;
	var clz32 = Math.clz32 || function(x) {return 31 - log(x >>> 0) / LN2 | 0};
	var fromCharCode = String.fromCharCode;
	var patchedU8Array = window.Uint8Array || Array;
	var TextEncoderPrototype = TextEncoder["prototype"];
	var GlobalTextEncoder = window["TextEncoder"];
	var globalTextEncoderPrototype;
	var globalTextEncoderInstance;
	//////////////////////////////////////////////////////////////////////////////////////
	function encoderReplacer(nonAsciiChars){
		// make the UTF string into a binary UTF-8 encoded string
		var point = nonAsciiChars.charCodeAt(0)|0;
		if (0xD800 <= point) {
			if (point < 0xDC00) {
				var nextcode = nonAsciiChars.charCodeAt(1)|0; // defaults to 0 when NaN, causing null replacement character
				
				if (0xDC00 <= nextcode && nextcode <= 0xDFFF) {
					//point = ((point - 0xD800)<<10) + nextcode - 0xDC00 + 0x10000|0;
					point = (point<<10) + nextcode - 0x35fdc00|0;
					if (point > 0xffff)
						return fromCharCode(
							(0x1e/*0b11110*/<<3) | (point>>>18),
							(0x2/*0b10*/<<6) | ((point>>>12)&0x3f/*0b00111111*/),
							(0x2/*0b10*/<<6) | ((point>>>6)&0x3f/*0b00111111*/),
							(0x2/*0b10*/<<6) | (point&0x3f/*0b00111111*/)
						);
				} else point = 65533/*0b1111111111111101*/;//return '\xEF\xBF\xBD';//fromCharCode(0xef, 0xbf, 0xbd);
			} else if (point <= 0xDFFF) {
				point = 65533/*0b1111111111111101*/;//return '\xEF\xBF\xBD';//fromCharCode(0xef, 0xbf, 0xbd);
			}
		}
		/*if (point <= 0x007f) return nonAsciiChars;
		else */if (point <= 0x07ff) {
			return fromCharCode((0x6<<5)|(point>>>6), (0x2<<6)|(point&0x3f));
		} else return fromCharCode(
			(0xe/*0b1110*/<<4) | (point>>>12),
			(0x2/*0b10*/<<6) | ((point>>>6)&0x3f/*0b00111111*/),
			(0x2/*0b10*/<<6) | (point&0x3f/*0b00111111*/)
		);
	}
	function TextEncoder(){};
	TextEncoderPrototype["encode"] = function(inputString) {
		// 0xc0 => 0b11000000; 0xff => 0b11111111; 0xc0-0xff => 0b11xxxxxx
		// 0x80 => 0b10000000; 0xbf => 0b10111111; 0x80-0xbf => 0b10xxxxxx
		var encodedString = inputString === void 0 ?  "" : ("" + inputString).replace(/[\x80-\uD7ff\uDC00-\uFFFF]|[\uD800-\uDBFF][\uDC00-\uDFFF]?/g, encoderReplacer);
		var len=encodedString.length|0, result = new patchedU8Array(len);
		var i=0;
		for (; i<len; i=i+1|0)
			result[i] = encodedString.charCodeAt(i) |0;
		return result;
	};
	function polyfill_encodeInto(inputString, u8Arr) {
		var encodedString = inputString === void 0 ?  "" : ("" + inputString).replace(/[\x80-\uD7ff\uDC00-\uFFFF]|[\uD800-\uDBFF][\uDC00-\uDFFF]?/g, encoderReplacer);
		var len=encodedString.length|0, i=0, char=0, read=0, u8ArrLen = u8Arr.length|0, inputLength=inputString.length|0;
		if (u8ArrLen < len) len=u8ArrLen;
		putChars: {
			for (; i<len; i=i+1|0) {
				char = encodedString.charCodeAt(i) |0;
				switch(char >>> 4) {
					case 0:
					case 1:
					case 2:
					case 3:
					case 4:
					case 5:
					case 6:
					case 7:
						read = read + 1|0;
						// extension points:
					case 8:
					case 9:
					case 10:
					case 11:
						break;
					case 12:
					case 13:
						if ((i+1|0) < u8ArrLen) {
							read = read + 1|0;
							break;
						}
					case 14:
						if ((i+2|0) < u8ArrLen) {
							read = read + 1|0;
							break;
						}
					case 15:
						if ((i+3|0) < u8ArrLen) {
							read = read + 1|0;
							break;
						}
					default:
						break putChars;
				}
				//read = read + ((char >>> 6) !== 2) |0;
				u8Arr[i] = char;
			}
		}
		return {"written": i, "read": inputLength < read ? inputLength : read};
		// 0xc0 => 0b11000000; 0xff => 0b11111111; 0xc0-0xff => 0b11xxxxxx
		// 0x80 => 0b10000000; 0xbf => 0b10111111; 0x80-0xbf => 0b10xxxxxx
		/*var encodedString = typeof inputString == "string" ? inputString : inputString === void 0 ?  "" : "" + inputString;
		var encodedLen = encodedString.length|0, u8LenLeft=u8Arr.length|0;
		var i=-1, read=-1, code=0, point=0, nextcode=0;
		tryFast: if (2 < encodedLen && encodedLen < (u8LenLeft >> 1)) {
			// Skip the normal checks because we can almost certainly fit the string inside the existing buffer
			while (1) {		// make the UTF string into a binary UTF-8 encoded string
				point = encodedString.charCodeAt(read = read + 1|0)|0;
				
				if (point <= 0x007f) {
					if (point === 0 && encodedLen <= read) {
						read = read - 1|0;
						break; // we have reached the end of the string
					}
					u8Arr[i=i+1|0] = point;
				} else if (point <= 0x07ff) {
					u8Arr[i=i+1|0] = (0x6<<5)|(point>>>6);
					u8Arr[i=i+1|0] = (0x2<<6)|(point&0x3f);
				} else {
					if (0xD800 <= point && point < 0xDC00) {
						nextcode = encodedString.charCodeAt(read)|0; // defaults to 0 when NaN, causing null replacement character
						
						if (0xDC00 <= nextcode && nextcode <= 0xDFFF) {
							read = read + 1|0;
							//point = ((point - 0xD800)<<10) + nextcode - 0xDC00 + 0x10000|0;
							point = (point<<10) + nextcode - 0x35fdc00|0;
							if (point > 0xffff) {
								u8Arr[i=i+1|0] = (0x1e<<3) | (point>>>18);
								u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>12)&0x3f);
								u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>6)&0x3f);
								u8Arr[i=i+1|0] = (0x2<<6) | (point&0x3f);
								continue;
							}
						} else if (nextcode === 0 && encodedLen <= read) {
							break; // we have reached the end of the string
						} else {
							point = 65533;//0b1111111111111101; // invalid replacement character
						}
					}
					u8Arr[i=i+1|0] = (0xe<<4) | (point>>>12);
					u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>6)&0x3f);
					u8Arr[i=i+1|0] = (0x2<<6) | (point&0x3f);
					if (u8LenLeft < (i + ((encodedLen - read) << 1)|0)) {
						// These 3x chars are the only way to inflate the size to 3x
						u8LenLeft = u8LenLeft - i|0;
						break tryFast;
					}
				}
			}
			u8LenLeft = 0; // skip the next for-loop 
		}
		
		
		for (; 0 < u8LenLeft; ) {		// make the UTF string into a binary UTF-8 encoded string
			point = encodedString.charCodeAt(read = read + 1|0)|0;
			
			if (point <= 0x007f) {
				if (point === 0 && encodedLen <= read) {
					read = read - 1|0;
					break; // we have reached the end of the string
				}
				u8LenLeft = u8LenLeft - 1|0;
				u8Arr[i=i+1|0] = point;
			} else if (point <= 0x07ff) {
				u8LenLeft = u8LenLeft - 2|0;
				if (0 <= u8LenLeft) {
					u8Arr[i=i+1|0] = (0x6<<5)|(point>>>6);
					u8Arr[i=i+1|0] = (0x2<<6)|(point&0x3f);
				}
			} else {
				if (0xD800 <= point) {
					if (point < 0xDC00) {
						nextcode = encodedString.charCodeAt(read = read + 1|0)|0; // defaults to 0 when NaN, causing null replacement character
						
						if (0xDC00 <= nextcode && nextcode <= 0xDFFF) {
							read = read + 1|0;
							//point = ((point - 0xD800)<<10) + nextcode - 0xDC00 + 0x10000|0;
							point = (point<<10) + nextcode - 0x35fdc00|0;
							if (point > 0xffff) {
								u8LenLeft = u8LenLeft - 4|0;
								if (0 <= u8LenLeft) {
									u8Arr[i=i+1|0] = (0x1e<<3) | (point>>>18);
									u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>12)&0x3f);
									u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>6)&0x3f);
									u8Arr[i=i+1|0] = (0x2<<6) | (point&0x3f);
								}
								continue;
							}
						} else if (nextcode === 0 && encodedLen <= read) {
							break; // we have reached the end of the string
						} else {
							point = 65533;//0b1111111111111101; // invalid replacement character
						}
					} else if (point <= 0xDFFF) {
						point = 65533/*0b1111111111111101*\/;//return '\xEF\xBF\xBD';//fromCharCode(0xef, 0xbf, 0xbd);
					}
				}
				u8LenLeft = u8LenLeft - 3|0;
				if (0 <= u8LenLeft) {
					u8Arr[i=i+1|0] = (0xe<<<4) | (point>>>12);
					u8Arr[i=i+1|0] = (0x2<<6) | ((point>>>6)&0x3f);
					u8Arr[i=i+1|0] = (0x2<<6) | (point&0x3f);
				}
			}
		} 
		return {"read": read < 0 ? 0 : u8LenLeft < 0 ? read : read+1|0, "written": i < 0 ? 0 : i+1|0};*/
	};
	if (ENCODEINTO_BUILD) {
		TextEncoderPrototype["encodeInto"] = polyfill_encodeInto;
	}
	if (!GlobalTextEncoder) {
		window["TextEncoder"] = TextEncoder;
	} else if (ENCODEINTO_BUILD && !(globalTextEncoderPrototype = GlobalTextEncoder["prototype"])["encodeInto"]) {
		globalTextEncoderInstance = new GlobalTextEncoder;
		globalTextEncoderPrototype["encodeInto"] = function(string, u8arr) {
			// Unfortunately, there's no way I can think of to quickly extract the number of bits written and the number of bytes read and such
			var strLen = string.length|0, u8Len = u8arr.length|0;
			if (strLen < ((u8Len >> 1) + 3|0)) { // in most circumstances, this means its safe. there are still edge-cases which are possible
				// in many circumstances, we can use the faster native TextEncoder
				var res8 = globalTextEncoderInstance["encode"](string);
				var res8Len = res8.length|0;
				if (res8Len < u8Len) { // if we dont have to worry about read/written
					u8arr.set( res8 );
					return {
						"read": strLen,
						"written": res8.length|0
					};
				}
			}
			return polyfill_encodeInto(string, u8arr);
		};
	}
})(typeof global == "" + void 0 ? typeof self == "" + void 0 ? this : self : global);
```

### 思考

其实对应的类库还是很多的比如`nodejs` 环境下的 `util` 的`TextEncoder`和`TextDecoder`

#### 拓展一

```javascript
"use strict"
exports.TextEncoder =
  typeof TextEncoder !== "undefined" ? TextEncoder : require("util").TextEncoder

exports.TextDecoder =
  typeof TextDecoder !== "undefined" ? TextDecoder : require("util").TextDecoder
```

参考地址：https://github.com/gozala/web-encoding

#### 拓展二

nodeJs版本低于v11，可以安装module`text-encodin`

```bash
npm install text-encoding 
```

```javascript
// These have been added in NodeJS v11, so good idea is to check first
if (typeof TextEncoder !== 'function') {
    const TextEncodingPolyfill = require('text-encoding');
    window.TextEncoder = TextEncodingPolyfill.TextEncoder;
    window.TextDecoder = TextEncodingPolyfill.TextDecoder;
}
```

