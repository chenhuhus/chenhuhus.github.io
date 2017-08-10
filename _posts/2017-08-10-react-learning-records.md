---
layout: post
title: "react-learning-records"
date: 2017-08-10
tag: JavaScript
---

##### 记录一些笔记 方便以后查阅

#### ## webpack

	webpack --watch
	webpack-dev-server
	webpack-dev-server --contentbase src --inline --hot  // 项目热加载 访问 http://localhost:8080/

#### ## Component

	class Index extends React.Component {
      render() {
        // render() 中用参数的形式
        var component;
        if(用户已登录) {
          component = <ComponentLoginedHeader/>;
        } else {
          component = <ComponentHeader/>;
        }
        return(
          // 组件的 return 函数返回的HTML节点必须是一个
          <div>
            {component}
            <BodyIndex/>
            <ComponentFooter/>
          </div>
        )
      }
	}

#### ## JSX

	class BodyIndex extends React.Component {
	  render() {
	    var userName = 'rukawa';
	    var boolInput = false;
	    // comments 空格转码
	    // 1. var html = 'rukawa\u0020rukawa';
	    // 2. dangerouslySetInnerHTML
	    var html = 'rukawa&nbsp;rukawa';
	    render() {
	      return(
	        <div>
	          <h1>BodyIndex here</h1>
	          {/*comments 三元表达式*/}
	          <p>{userName == '' ? '用户未登录' : '用户名:' + userName}</p>
	          <input type='button' value={userName} disabled={boolInput}/>
	          <p>{html}</p> {/*1.*/}
	        </div>
	      )
	    }
	  }
	}

#### ## The Component Lifecycle

##### componentWillMount()

	It is called before render()

##### componentDidMount()