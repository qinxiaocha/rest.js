# rest.js

```
/* RESTFUL APIs
    - 包含跟服务器交互的常用方法
    @param path 请求的url
    @param data 前端传递给后台的数据对象
    @param params 查询对象，将被parse成类似"?a=1&b=2"的形式拼在path后面
   共包含五种方法：
    Rest#post     - 增
    Rest#delete   - 删
    Rest#put      - 改
    Rest#get      - 查
    Rest#upload   - 上传

   Usage:
    var rest = require('rest');

    rest.post('user/new', {
      name: 'jacket',
      position: 'Frontend Developer'
    }).done(function (res) {
      // Do something...
    });
*/
import helper from './helper'

function Rest() {}

Rest.prototype = {
  // 增 - POST
  post: function(path, data, params) {
    if (params) path = parse(path, params);
    return doAjax('POST', path, data);
  },
  // 删 - DELETE
  delete: function(path, data, params) {
    if (params) path = parse(path, params);
    return doAjax('DELETE', path, data);
  },
  // 改 - PUT
  put: function(path, data, params) {
    if (params) path = parse(path, params);
    return doAjax('PUT', path, data);
  },
  // 查 - GET
  get: function(path, params) {
    if (params) path = parse(path, params);
    return doAjax('GET', path);
  },
  // 上传 - POST
  upload: function(path, data, params) {
    if (params) path = parse(path, params);
    return uploadHelper(path, data);
  }
}

/* 格式化请求路径
    @param path 请求路径
    @param params 查询对象或字符串
  Usage:
    parse('user/query', '?name=j&age=21')
    or
    parse('user/query', { name: 'j', age: '21' })
  将输出：'user/query?name=j&age=21'
*/
function parse(path, params) {
  if (path.charAt(path.length - 1) == '/') path = path.substr(0, path.length - 1);
  if (helper.isString(params)) return path + '/' + params;
  if (helper.isObject(params)) {
    var search = '?',
      counter = 0;
    for (var key in params) {
      if (counter) search += '&';
      search += key + '=' + params[key];
      counter++;
    }
    return path + search;
  }
  return path;
}

function doAjax(type, path, data, contentType, dataType) {
  var setting = apiSetting(type, path, contentType, dataType);
  if (data) {
    if (type.toLowerCase() == 'post' || type.toLowerCase() == 'put') data = JSON.stringify(data);
    setting['data'] = data;
    //setting['data'] = JSON.stringify(data);

  }
  return $.ajax(setting);
}

function uploadHelper(path, data) {
  var setting = apiSetting('POST', path);
  setting['data'] = data;
  setting['contentType'] = false;
  setting['processData'] = false;
  return $.ajax(setting);
}

function apiSetting(type, path, contentType, dataType) {
  var baseUrl = '_API_';
  var setting = {
    cache: false,
    url: baseUrl + path,
    type: type,
    contentType: 'application/json',
    dataType: 'json',
    // contentType: contentType || 'application/x-www-form-urlencoded',
    // dataType: dataType || 'json',
    timeout: 1000 * 60,
    crossDomain: true,
    beforeSend: function(xhr) {
      //$('#processing').removeClass('hide');
    },
    complete: function(xhr) {
      //$('#processing').addClass('hide');
    },
    success: function(res) {

    },
    error: function(xhr, textStatus) {
      if (xhr.status === 404) {

      } else if (xhr.status === 500) {

      } else if (xhr.status === 0) {

      } else if (textStatus === 'parsererror') {

      } else if (textStatus === 'timeout') {

      } else {

      }
    }
  }
  if (helper.get('token') && helper.get('token') !== 'null') {
    if (!setting['headers']) setting['headers'] = {};
    setting['headers']['X-Token'] = helper.get('token');
  }


  return setting;
}

const rest = new Rest;

export default rest;

```
