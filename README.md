# React-Native-Navigator-NavigatorIOS-Demo<br>
由于ReactNative中，Navigator是整个初学阶段的最难点之一，官网中列出来的示例也不够详尽，所以我在此总结并扩展一下Navigator的用法。<br>
Navigator分为两种，一种是通用的Navigator，另一种是IOS专属的NavigatorIOS。从使用难易程度上来讲，NavigatorIOS要比Navigator简单不少，在IOS上相当于直接集成了NavigationBar的Navigator。<br>
先上NavigatorIOS的代码：<br>


    class Hello extends Component {

    render() {
        return (
           <NavigatorIOS initialRoute={{
               component:FirstPage,
               title:'导航',
               rightButtonTitle:'更多',
               onRightButtonPress:this.onRightButtonPress,
           }}
           ref='nav'
           style={{flex:1}}
           />
        );
    }
    onRightButtonPress=()=>{
        this.refs.nav.push({
            title:'Second Page',
            component:SecondPage
        })
    }

    }
    class FirstPage extends Component{
        render(){
            return(
                <View style={{flex:1}}>
                <Text style={{marginTop:80}} onPress={()=>{
                    this.props.navigator.push({
                        title:'From Right 2',
                        component:SecondPage,
                    });
                }}>这是第一页</Text>
                </View>

            );
        }
    }
    class SecondPage extends  Component{
        render(){
            return <Text style={{marginTop:80}} >这是第二页</Text>
        }

    }
NavigatorIOS只有一个必选属性：initialRoute。title相当于在NavigationBar上的标题，这个是自定义的。如果有rightButtonTitle这个属性，则会自动在右侧生成一个可以点击的按钮，onRightButtonPress就是这个按钮点击事件的回调。另外值得注意的是，这个控件必须指明一个宽高或者flex，否则component中的内容将不会显示，而且NavigatorBar上的按钮也无法点击。<br>
然后看一下Navigator和NavigatorBar的代码：<br>


    const pages= [
        'page1','page2','page3','page4'
    ]
    const routeMappers={
        LeftButton:(route,navigator,index,navState)=>{
            return <Text onPress={()=>{
                navigator.pop();
                i--;
            }
            }>Back</Text>
        },
        Title:(route,navigator,index,navState)=>{
            return <Text>lalala</Text>
        },
        RightButton:(route,navigator,index,navState)=>{
            return <Text>RightButton</Text>
        }
    }

    var i=0;
    class Hello extends Component {
       
        render() {

        return (
            <Navigator
                initialRoute={
                    pages[i]
                }
                initialRouteStack={
                    pages
                }
                configureScene={(route,routeStack)=>{
                    return Navigator.SceneConfigs.HorizontalSwipeJump;
                }}
                renderScene={(route,navigator)=>
                    <TouchableOpacity style={{marginTop:100}}
                                      onPress={()=>{
                                          navigator.push(images[++i]);
                                      }}
                    >
                        <Text>{route}下一页</Text>
                    </TouchableOpacity>
                }

                navigationBar={
                    <Navigator.NavigationBar
                        routeMapper={routeMappers}
                        style={{backgroundColor:'red',height:60}}
                    />
                }
            />
        );
    }
    }
<br>
首先Navigator有两个必要属性，initialRoute和renderScene。initialRoute的作用是初始化路由，也就是初始化数据。这里的路由实际上就是在不同页面之间传递的数据。initialRouteStack是指初始化一个路由栈，如果不提供这个属性，则会默认生成一个只有一个数据的路由栈，而这个数据就是来源于initialRoute的数据。如果提供了这个属性，我们也可以直接通过路由站，routeStack的jumpToIndex属性直接跳转页面。<br>其次，NavigationBar是Navigator的一个内部类，routeMapper用来定义LeftButton,Title,RightButton。
