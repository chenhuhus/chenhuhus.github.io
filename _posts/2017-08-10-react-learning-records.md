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

#### ## State && Props

##### State 对于模块 属于自身属性

##### Props 对于模块 属于外来属性 父向子传参

子页面

	export default class BodyIndex extends React.Component {
	  constructor() {
	    super();  // 调用基类的所有初始化方法
	    this.state = {
	      userName: 'rukawa',  // 初始化赋值
	      age: 11
	    }  // 每个 state 只用于管理当前 class, 不会污染到其他 class
	  }

	  render() {
	    setTimeout(()=> {
	      this.setState({userName: 'cute'})
	    }, 2000)

	    return (
	      <div>
	        <h1>BodyIndex here</h1>
	        <p>{this.state.userName}{this.state.age}</p>
	        <p>{this.props.userid}{this.props.username}</p>
	        <Child {...this.props} id={77}/>
	        {/*子把父传过来的属性全部继续传给孙，且可以新定义*/}
	      </div>
	    )
	  }
	}

父页面

	import BodyIndex from './components/BodyIndex';
	class Index extends React.Component {
	  render() {
	    return(
	      <div>
	        <ComponentHeader/>
	        <BodyIndex userid={123} username={'rukkk'}/>
	        <ComponentFooter/>
	      </div>
	    )
	  }
	}



##### 子向父传参: 只能通过事件 调用父传过来的事件的 props

子页面

	export default class BodyChild extends React.Component {
	  render() {
	    return (
	      <div>
	        <p>子页面输入：<input type='text' onChange = {this.props.handleChildValueChange}/></p>
	      </div>
	    )
	  }
	}

父页面

	import BodyChild from './bodychild';
	class BodyIndex extends React.Component {
	  constructor() {
	    super();
	    this.state = {
	      age: 11
	    }
	  };

	  changeUserInfo(age) {
	    this.setState({age: age});
	  };

	  handleChildValue(event) {
	    this.setState({age: event.target.value})
	  };

	  render() {
	    return (
	      <div>
	        <input type='button' value='click' onClick={this.changeUserInfo.bind(this,666)}/>
	        <BodyChild handleChildValueChange={this.handleChildValue.bind(this)}/>
	      </div>
	    )
	  }
	}

##### Typechecking With PropTypes

	import PropTypes from 'prop-types';
	export default class BodyIndex extends React.Component {
	  render(){}
	}

	BodyIndex.propTypes = {
	  userid: PropTypes.number,
	  userid: PropTypes.number.isRequired,
	  username: PropTypes.string
	}

	BodyIndex.defaultProps = {
	  username: 'defaultname'
	}

##### Refs

ReactDOM.findDOMNode(component) // 不推荐