---
layout: post
title: "react-learning-records"
date: 2017-08-10
tag: JavaScript
---

##### 记录一些笔记 方便以后查阅

#### ##

	webpack --watch
	webpack-dev-server
	webpack-dev-server --contentbase src --inline --hot  // 项目热加载 访问 http://localhost:8080/

	babel-plugin-react-html-attrs ==> react中使用class 而不用必须使用className

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

##### Style

`'src\js\components\header.js'`

	export default class ComponentHeader extends React.Component {
	  constructor() {
	    super();
	    this.state = {
	      miniHeader: false
	    }
	  };

	  switchHeader() {
	    this.setState({
	      miniHeader: !this.state.miniHeader
	    })
	  };

	  render() {
	    const styleComponentHeader = {
	      header: {
	        backgroundColor: '#eee',
	        color: 'pink',
	        {/*内联样式中的表达式*/}
	        paddingTop: (this.state.miniHeader) ? '2px' : '15px',
	        paddingBottom: '15px'
	      }
	    }

	    return (
	      <header onClick = {this.switchHeader.bind(this)} style={styleComponentHeader.header}>
	        <h1>header here</h1>
	      </header>
	    )
	  }
	}

这种写法会以内联样式显示，动画/hover伪类不能使用，react中不推荐这种写法，但RN中为此写法

`CSS 模块化` 1. 全局污染 2. 命名混乱 3. 依赖管理不彻底 4. 无法共享变量 5. 代码压缩不彻底

`'webpack.config.js'` 中配置

	module: {
	  loaders: [
	    {
	      test: /\.css$/,
        loader: 'style!css-loader?modules&importLoaders=1&localIdentName=[name]__[local]___[hash:base64:5]'
	    }
	  ]
	}

`'src\js\components\footer.js'`

	import footerCss from '../../css/footer.css';

	export default class ComponentFooter extends React.Component {
	  render() {
	    return (
	      <footer className={footerCss.miniFooter}>
	        <h1>footer here</h1>
	      </footer>
	    )
	  }
	}

`'../../css/footer.css'`

	.miniFooter {
	  background: #333;
	  color: #fff;
	  padding-left: 20px;
	  padding-top: 3px;
	  padding-bottom: 3px;
	}

	.miniFooter h1 {
	  font-size: 15px;
	}