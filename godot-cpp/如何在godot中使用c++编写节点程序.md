# 如何在godot中使用c++编写节点程序?

### <p>提示:</p>
### <p>本文档使用大标题与小标题划分大板块,使用引用块表示介绍</p>
### <p>使用无处理的文本表示正文内容,使用代码块表示命令、指令、程序</p>
### <p>使用斜体表示示例</p>
### <p>使用①等表示大并列,使用1、等表示其的子并列
### <p>注意:</p>
### <p>以下操作仅支持64位windows平台</p>

## 问题导入

<p>在godot中,实现一些物体的平动,转动,需要用到程序来控制它们的运动(即使用脚本)</p>
<p>在godot选中一个节点,并开始设置节点的脚本(程序),可以发现,godot仅支持其自身gdscript语言</p>
<p>这给许多了解cpp但不了解gdscript语言的人带来困难</p>
<p>故此,希望使用c++编写节点程序实现对节点的运动控制</p>

## 解决方案

<p>①godot官方在github上的gdextension插件可以实现这个功能
<p>参见:https://github.com/godotengine/godot-cpp</p>

***

<p>②godot使用命令提示符生成一个文件,即extension_api,json需要使用这个插件判断godot版本</p>
<p>交互式的godot应用程序本身不具备这个能力,因此需要在windows的命令提示符处生成</p>

><p>这里简单介绍一下环境变量</p>
><p>命令提示符相关的环境变量的要素是路径,修改其的位置在windows的设置中的控制面板中的高级系统设置中的环境变量中的系统变量中的Path,以及同位置的PATHEXT,这个PATHEXT里面包含了文件后缀(文件后缀=.+拓展名)</p>
><p>1、当把一个路径添加到环境变量Path后,如果希望在控制台启动一个程序,命令提示符会自动搜索环境变量里的路径里的程序并启动</p>
><p>2、如果在里面加入对应文件后缀,使用半角分号";"分隔,那么在命令提示符启动程序时,可以不用输入文件后缀,输入文件名即可打开;对于某些程序,在控制台启动该程序可以额外添加命令,实现交互式应用程序无法实现的一些操作</p>
><p>特别注意,依赖命令提示符打开的程序,在命令提示符关闭后也会随之关闭</p>

<p><em>比如,希望启动c盘下的文件夹a里的b.exe,那么需要在环境变量Path中添加C:\a,修改之后,在控制台输入b.exe,即可直接启动b.exe</em></p>

<p>基于此,可以在godot程序的文件夹生成(创建)一个godot.windows.opt.tools.64.exe的快捷方式,并将其命名为godot(特别注意,所有快捷方式的文件后缀是.LNK,只不过windows一般不显示,不代表其没有文件后缀),再在PATHEXT中添加.LNK,点击确定,就可以在命令提示符输入godot直接启动godot</p>
<p>这个时候,在命令提示符输入以下指令:</p>

    godot --dump-extension-api

<p>等待执行完毕后,可以发现godot软件的目录里面出现了一个extension_api.json文件</p>

***

<p>③gdextension插件通过工具scons发挥作用,因此需要scons工具</p>
<p>scons必须使用python中的命令pip安装,因此必须确保电脑中有python</p>
<p>参见:https://www.python.org/ftp/python/3.13.1/python-3.13.1-amd64.exe</p>
<p>特别注意,安装python时需要在安装程序中勾选自动配置环境变量的选项,并且勾选optional features中的pip选项,这样才能在命令提示符中使用pip指令</p>
<p>同时特别注意,必须以管理员身份运行命令提示符,才能正常安装scons</p>
<p>所以来到C:\Windows\System32\cmd.exe,并以管理员身份运行其,键入pip install scons,等待显示安装完成即可</p>

><p>同时简单介绍一下scons,其可以类比cmake,那么cmake又是什么</p>
><p>暂时可以片面地理解为编译器,是一个visual studio 2022去除了项目构建、项目编辑、项目调试的软件</p>

## 操作执行

><p>先简单介绍一下命令提示符的机制和scons的使用</p>
><p>1、进入命令提示符后,窗口显示的路径是命令提示符程序操作的路径</p>
><p>即如果进入了C:\a,如果在没有特别设置环境变量的情况下,这个命令提示符窗口就只能对a文件夹内文件进行操作,而不能对其他任何文件夹内的文件进行操作</p>
><p>进入命令提示符,可以通过以下命令,对命令提示符操控的路径进行变换(其中%path%是文件所在的文件夹路径)</p>

    cd /d %path%

><p>2、对于scons,需要在进行编译的文件所在的文件夹内(相同路径),编写一个Sconstruct文件(使用.txt纯文本编写,无文件后缀,所以在编写完之后记得主动去掉文件后缀),然后将命令提示符的操作路径转换到该文件夹,输入命令</p>

    scons

><p>提示搭建完毕后,文件夹内会产生一个与被编译文件同名的.obj和.exe文件,以及一个.sconsign.dblite文件(意义不明)</p>
><p>那么如何编写呢?</p>

<em><p>对于一个最简单的cpp的helloworld文件:</em></p>

    #include<stdio.h>
    int main()
    {
        printf("Hello,world!");
        return 0;
    }

<em><p>将其以纯文本形式(使用.txt编写)命名为hello.cpp后,编写Sconstruct如下</em></p>

    Program('hello.cpp')

<em><p>这段命令包含两个要素,一是Program,提示scons对括号与单引号内的文件进行编译</em></p>
<em><p>二是*.cpp,提示scons对文件夹内的哪个文件进行编译</em><p>
<em><p>接下来,进入命令提示符,可以通过以下命令,对命令提示符操控的路径进行变换</em></p>
<em><p>假设该hello.cpp程序位于C:\Users\*此处应有用户名*\Desktop\hello中,于是有</em></p>

    cd /d C:\Users\*此处应有用户名*\Desktop\hello

<em><p>注意将Sconstruct文件与hello.cpp文件都放在桌面的hello文件夹内</em></p>
<em><p>切换了命令提示符的操作路径后,输入以下指令</em></p>

    scons

<em><p>当命令提示符显示以下语句后,说明编译完成</em></p>

    scons: Reading SConscript files ...
    scons: done reading SConscript files.
    scons: Building targets ...
    cl /Fohello.obj /c hello.cpp /TP /nologo
    hello.cpp
    link /nologo /OUT:hello.exe hello.obj
    scons: done building targets.

<em><p>编译完成后,文件夹内额外会产生hello.exe与hello.obj与.sconsign.dblite文件</em></p>

<p>①对于解决方案②中获取的extension_api.json文件,使用记事本打开这个文件,可以看到文件的第三行和第四行,分别有version的major和minor</p>
<p>major=a与minor=b代表其处在a.b版本,记录版本

***

<p>②下载了解决方案①的gdextension插件后,将其命名为godot-cpp,打开gdextension文件夹,发现其内也有一个extension_api.json文件,使用记事本打开,同样在第三行第四行确定版本信息</p>
<p>必须确保版本信息一至才能实现godot节点脚本(程序)的cpp编写</p>
<p>(当然对release方没有太大要求,无论是steam还是official都可以)</p>

<em><p>比如4.3steam版本就不能用4.4官方版本</em></p>

***

<p>③编写脚本(程序),在godot中,脚本(程序)是用来控制节点行为的一系列指令和命令</p>
<p>脚本(程序)和节点是同级关系,每个节点都对应着一个或多个类</p>
<p>但是使用gdextension插件时,必须从想继承的目标类新建一个子类(子节点)</p>
<p>继续调用这个子类(子节点)</p>
<p>换言之,想实现节点1的平动或者转动,使用gdscript时,只需要直接为其添加脚本(程序)</p>
<p>但是使用cpp编写,必须新建节点1的子类(子节点),继承节点1的特征,再在新节点上添加特征</p>
<p>基于此,假设继承godot已有的a类,想在a类的基础上继续操控节点,则有</p>

    class b:a
    {
        此处应有希望实现的功能对应的语句;
    };

<p>将所有脚本(程序)使用一个文件夹名为src装起来(名字只能为src),并建立一个大文件夹,将文件夹src,godot内创建的游戏项目文件夹,以及解决方案①下载的插件一并装入其中,同时注意,这个插件应该被命名为godot-cpp(如果本来不是这个文件夹名)</p>
<p>现在,这个大文件夹拥有三个小文件夹,分别是src、godot-cpp、游戏项目文件夹</p>

><p>godot的cpp脚本(程序)编写主要分为两大板块四大小板块,分别是注册板块和实现板块,每个大板块各有一个源文件、一个头文件</p>
><p>对于实现板块,可以在这里面进行节点(类)的继承、定义,真正让目标动起来</p>
><p>对于注册板块,这方面是和godot作对接用的</p>
><p>下面⑥会提到,需要使用.gdextension文件告诉(提示)godot该使用哪个文件来对接cpp</p>
><p>如果说.gdextension的文件是提示作用,那么对接文件就是使godot真正可以使用那些用cpp写出来的程序(脚本),使这些cpp程序(脚本)有资格在godot中发挥作用</p>
><p>官方文档称之为"注册新类"</p>
><p>由于注册板块的编写较为单一,这里直接给出程序</p>
><p>对于注册板块头文件register_types.h,有

    #ifndef GDEXAMPLE_REGISTER_TYPES_H
    #define GDEXAMPLE_REGISTER_TYPES_H
 
    #include <godot_cpp/core/class_db.hpp>
 
    using namespace godot;
 
    void initialize_example_module(ModuleInitializationLevel p_level);
    void uninitialize_example_module(ModuleInitializationLevel p_level);
 
    #endif // GDEXAMPLE_REGISTER_TYPES_H

><p>对于注册板块源文件register_types.cpp,有</p>

    #include "register_types.h"
 
    #include "此处应有实现板块头文件.h"
 
    #include <gdextension_interface.h>
    #include <godot_cpp/core/defs.hpp>
    #include <godot_cpp/godot.hpp>
 
    using namespace godot;
 
    void initialize_example_module(ModuleInitializationLevel p_level)
    {
	    if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE)
        {
	    	return;
	    }
    
	    ClassDB::register_class<此处应有希望实现的子节点(类)的名称>();
    }
 
    void uninitialize_example_module(ModuleInitializationLevel p_level)
    {
	    if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE)
        {
	    	return;
	    }
    }
 
    extern "C" 
    {
    // Initialization.
        GDExtensionBool GDE_EXPORT example_library_init(GDExtensionInterfaceGetProcAddress p_get_proc_address, const GDExtensionClassLibraryPtr p_library, GDExtensionInitialization *r_initialization)
        {
	        godot::GDExtensionBinding::InitObject init_obj(p_get_proc_address, p_library, r_initialization);
 
	        init_obj.register_initializer(initialize_example_module);
	        init_obj.register_terminator(uninitialize_example_module);
	        init_obj.set_minimum_library_initialization_level(MODULE_INITIALIZATION_LEVEL_SCENE);
 
	        return init_obj.init();
        }
    }

><p>实现板块的注意事项</p>
><p>1、对于实现板块的编写,根据cpp primer plus第305页,不应将函数(方法)的定义直接写到头文件内,而是在源文件内编写函数(方法)的定义,头文件中只负责定义类,并且使被定义的类包含这些函数(方法)</p>

<em><p>这在上面给出的注册板块中也被体现了(initialize_example_module函数(方法)和uninitialize_example_module函数(方法))</em></p>
<em><p>又例如,如果在头文件中包含一个函数定义,然后在其他两个文件(属于同一个程序)中包含该头文件,则同一个程序中将包含同一个函数的两个定义,除非函数是内联的(?),否则这将出错</em></p>

><p>2、上面给出的程序中包含了#ifndef、#define、#endif宏定义,一般熟知的宏定义是使用#define来使得一个式子与一个常量绑定

<em><p>比如#define a 1会使得该程序在其下面的语句将a替换成整数1</em></p>

><p>但是这里没有给#define后面的式子一个常量,这是符合语法的,并且这种用法一般使#define和#ifndef和#endif一并,并且#ifndef与#define后面的式子相同,在头文件的编写中出现,目的是防止头文件被多次调用
><p>#ifndef的意思是if not defined,这句话的意思是,如果ifndef后面的式子没有被定义,那么就执行#ifndef与#endif之内的语句,来看下面的例子辅助理解

<em><p>假设头文件a.h使用#ifndef、#define、#endif包含起来,则对于属于同一个程序中的两个文件b、c,如果在编写b与c时不小心犯错,在b与c中都写入了#include<a.h></em></p>
<em><p>假设程序先执行文件b,b调用头文件a.h,执行#ifndef与#endif之间的语句,这将使得#define后面的式子被定义</em><p>
<em><p>现在程序运行到c文件,继续(第二次)执行#include<a.h>,开始运行第一句#ifndef,发现其后面的式子已经被定义过了,于是不运行#ifndef与#endif之间的语句,也即防止了头文件的重复调用</em></p>
<em><p>同理假设头文件a.h没有使用这三个包含起来,那么运行到c文件时,会第二次运行a.h里面的语句,这会造成与1、中提到的"同一个程序包含同一个类"的程序错误</em></p>

><p>所以,在头文件的编写中,#ifndef、#define、#endif的组合必不可少,但是至于#ifndef与#define之后接什么,这个是任意的,确保#ifndef与#define之后的式子相同即可,不必为这个式子赋予常量
><p>在实现模块的头文件的编写中,需要特别注意这一点

***

<p>④测试编译,注意到godot-cpp文件夹内存在sconstruct文件</p>
<p>现在使用命令提示符的cd命令,将命令提示符的操作区域转换到godot-cpp文件夹</p>
<p>输入以下命令</p>

    scons platform=windows

<p>等待编译完成,提示如下语句后,表示测试编译完成

    scons: done building targets.

<p>同时,可以发现godot-cpp文件夹下的bin文件夹有文件:</p>
<p>libgodot-cpp.windows.template_debug.x86_64.lib</p>
<p>这同时也代表测试编译通过</p>

***

<p>⑤正式编译,前面有提到scons需要sconstruct文件驱动编译</p>
<p>现在需要对整个大文件夹(包含godot-cpp、src、游戏项目)进行编译,如何编写sconstruct?</p>
<p>有如下程序:</p>

    import os
    import sys

    env = SConscript("godot-cpp/SConstruct")
    env.Append(CPPPATH=["src/"])

    sources = Glob("src/*.cpp")

    library = env.SharedLibrary
    (
        "demo/bin/lib%此处应有文件名%{}{}".format(env["suffix"], env["SHLIBSUFFIX"]),
        source=sources,
    )

    Default(library)

<p>编写完毕后,命名为sonstruct,将其存放在与godot-cpp、游戏项目、src三个文件夹同级的地方</p>
<p>也即放在大文件夹下,然后将命令提示符的范围切换到大文件夹,输入如下命令:</p>

    scons platform=windows

<p>等待提示编译完毕,来到游戏项目文件夹,就可以发现多出来一个bin文件夹,里面若有文件:</p>
<p>lib%此处应有文件名%.windows.template_debug.x86_64.dll</p>
<p>和与之同名的.exp、.lib文件,则表示src中的*.cpp文件已经成功编译,scons将其成功化作.dll文件供godot使用</p>

***

<p>⑥最后还需要提示godot调用这个dll文件,有如下程序:</p>

    [configuration]

    entry_symbol = "example_library_init"
    compatibility_minimum = "4.2"

    [libraries]

    windows.debug.x86_64 = "res://bin/lib%此处应有文件名%.windows.template_debug.x86_64.dll"
    windows.release.x86_64 = "res://bin/lib%此处应有文件名%.windows.template_release.x86_64.dll"

<p>将这个程序使用纯文本编写,命名为"%此处应有文件名%.gdextension",并将其放置在游戏项目文件夹下的bin文件夹内,使之与lib%此处应有文件名%.windows.template_debug.x86_64.dll平行同级</p>
<p>最后打开godot,来到创建节点的窗口,搜索%此处应有文件名%,即可找到自定义的子类(子节点)</p>
<p>注意:scons在正式编译的时候,如果调用以上程序,其只会对src文件夹内的所有后缀为.cpp的文件进行编译</p>

## 例子(默认读者会找文件路径和解压)

<em><p>打开浏览器首先来到https://store.steampowered.com/about</p>
<p>下载一个steam,注册一个账号,登录这个账号,打开steam,在主界面点左上角的商店,在商店页面右上角的搜索键入godot,点击弹出来的godot engine框,进去下载安装了</p>
<p>steam左上角点一下库,在游戏库中找到godot engine,右键它,选择"属性",在弹出来的窗口中选择"已安装文件",在新页面点"浏览…",找到godot.windows.opt.tools.64.exe这个文件,右键它,选择"属性",弹窗里选择"安全",复制"对象名称"</p>
<p>打开"此电脑"里的c盘的windows文件夹的system32文件夹的cmd.exe文件,在弹窗中输入cd /d,然后加一个空格粘贴刚才复制到的对象名称,然后加一个空格输入--dump-extension-api,回车,等个十秒,然后回到刚才在steam的"浏览…"中打开的文件夹,对extension_api.json文件右击,点"打开方式",点"更多应用",选择"记事本",记录第三第四行version_major与version_minor的数字</p>
<p>打开浏览器去https://github.com/godotengine/godot-cpp</p>
<p>左上角找到branchs图标,点击,选择一个与version_major.version_minor(这是一个小数)相符的数字,然后在正右边绿色的code点一下,点击download zip,下载之后解压,将解压出来的文件夹右击,重命名为godot-cpp</p>
<p>打开浏览器去https://www.python.org/ftp/python/3.13.1/python-3.13.1-amd64.exe下载一个python,记得把下面的add python to path勾了,点install now,等待</p>
<p>等安装界面显示setup was successful之后,去到上面的cmd.exe,右键以管理员身份运行,进去输入pip install scons,等待安装完成</p>
<p>在任意一个位置创建一个空文件夹,去steam库打开godot,弹出来的窗口中左上角点击创建,然后在"浏览"中找到这个空文件夹,使项目路径和空文件夹路径相同,任意命名项目名称,其他不用管,点击创建并编辑</p>
<p>回到刚才的"空文件夹"(现在不空),把刚才解压并重命名的godot-cpp文件右键一下,点复制,然后回到"空文件夹",右键"空文件夹"界面,选择"粘贴",然后右键空文件夹界面,选择"新建",选择"文件夹",在文件夹名中键入"src"</p>
<p>在文件夹左上角界面点"查看",点"选项,点"更改文件夹和搜索选项",弹出来的窗口中点"查看","高级设置"栏往下划,取消勾选"隐藏已知文件类型的拓展名",右下角点"应用",然后右下角点"确定"
<p>打开刚才"空文件夹"里面的src文件夹,右键界面,选择新建,选择文本文档,打开这个文本文档,复制以下指令,在文本文档界面右击,选择粘贴</em></p>

```
#ifndef GDEXAMPLE_H
#define GDEXAMPLE_H

#include <godot_cpp/classes/sprite2d.hpp>

namespace godot {

class GDExample : public Sprite2D {
	GDCLASS(GDExample, Sprite2D)

private:
	double time_passed;

protected:
	static void _bind_methods();

public:
	GDExample();
	~GDExample();

	void _process(double delta) override;
};

}

#endif
```

<em><p>文档界面左上角点"文件",点保存,然后对这个文本文档右键,重命名为gdexample.h</p>
<p>同理创建其他三个文本文档,分别键入以下三块指令并保存,然后重命名,名字分别为gdexample.cpp、register_types.cpp、register_types.h</em></p>

```
#include "gdexample.h"
#include <godot_cpp/core/class_db.hpp>

using namespace godot;

void GDExample::_bind_methods() {
}

GDExample::GDExample() {
	// Initialize any variables here.
	time_passed = 0.0;
}

GDExample::~GDExample() {
	// Add your cleanup here.
}

void GDExample::_process(double delta) {
	time_passed += delta;

	Vector2 new_position = Vector2(10.0 + (10.0 * sin(time_passed * 2.0)), 10.0 + (10.0 * cos(time_passed * 1.5)));

	set_position(new_position);
}
```

```
#include "register_types.h"

#include "gdexample.h"

#include <gdextension_interface.h>
#include <godot_cpp/core/defs.hpp>
#include <godot_cpp/godot.hpp>

using namespace godot;

void initialize_example_module(ModuleInitializationLevel p_level) {
	if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE) {
		return;
	}

	GDREGISTER_RUNTIME_CLASS(GDExample);
}

void uninitialize_example_module(ModuleInitializationLevel p_level) {
	if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE) {
		return;
	}
}

extern "C" {
// Initialization.
GDExtensionBool GDE_EXPORT example_library_init(GDExtensionInterfaceGetProcAddress p_get_proc_address, const GDExtensionClassLibraryPtr p_library, GDExtensionInitialization *r_initialization) {
	godot::GDExtensionBinding::InitObject init_obj(p_get_proc_address, p_library, r_initialization);

	init_obj.register_initializer(initialize_example_module);
	init_obj.register_terminator(uninitialize_example_module);
	init_obj.set_minimum_library_initialization_level(MODULE_INITIALIZATION_LEVEL_SCENE);

	return init_obj.init();
}
}
```

```
#ifndef GDEXAMPLE_REGISTER_TYPES_H
#define GDEXAMPLE_REGISTER_TYPES_H

#include <godot_cpp/core/class_db.hpp>

using namespace godot;

void initialize_example_module(ModuleInitializationLevel p_level);
void uninitialize_example_module(ModuleInitializationLevel p_level);

#endif // GDEXAMPLE_REGISTER_TYPES_H
```

<em><p>继续去到cmd.exe,找到"空文件夹"下的godot-cpp文件夹的路径,复制了,在cmd.exe窗口里面输入cd /d,然后空格,然后粘贴,然后回车,然后输入scons,等待显示scons: done building targets.</p>
<p>打开浏览器去https://docs.godotengine.org/zh-cn/4.x/_downloads/45a3f5e351266601b5e7663dc077fe12/SConstruct</p>
<p>将这个下载好的东西右键复制,回到"空文件夹"窗口,右键粘贴,然后复制"空文件夹"路径,回到cmd.exe窗口,输入cd /d,然后空格,然后粘贴,然后回车,然后输入scons,等待显示scons: done building targets.</p>
<p>接着,复制以下命令,在游戏项目文件夹内的bin文件夹页面右键,新建文本文档,粘贴,文档左上角文件选项里面点保存,然后出来右击文件图标,重命名输入gdexample.gdextension</p>

```
[configuration]

entry_symbol = "example_library_init"
compatibility_minimum = "4.1"
reloadable = true

[libraries]

macos.debug = "res://bin/libgdexample.macos.template_debug.framework"
macos.release = "res://bin/libgdexample.macos.template_release.framework"
ios.debug = "res://bin/libgdexample.ios.template_debug.xcframework"
ios.release = "res://bin/libgdexample.ios.template_release.xcframework"
windows.debug.x86_32 = "res://bin/libgdexample.windows.template_debug.x86_32.dll"
windows.release.x86_32 = "res://bin/libgdexample.windows.template_release.x86_32.dll"
windows.debug.x86_64 = "res://bin/libgdexample.windows.template_debug.x86_64.dll"
windows.release.x86_64 = "res://bin/libgdexample.windows.template_release.x86_64.dll"
linux.debug.x86_64 = "res://bin/libgdexample.linux.template_debug.x86_64.so"
linux.release.x86_64 = "res://bin/libgdexample.linux.template_release.x86_64.so"
linux.debug.arm64 = "res://bin/libgdexample.linux.template_debug.arm64.so"
linux.release.arm64 = "res://bin/libgdexample.linux.template_release.arm64.so"
linux.debug.rv64 = "res://bin/libgdexample.linux.template_debug.rv64.so"
linux.release.rv64 = "res://bin/libgdexample.linux.template_release.rv64.so"
android.debug.x86_64 = "res://bin/libgdexample.android.template_debug.x86_64.so"
android.release.x86_64 = "res://bin/libgdexample.android.template_release.x86_64.so"
android.debug.arm64 = "res://bin/libgdexample.android.template_debug.arm64.so"
android.release.arm64 = "res://bin/libgdexample.android.template_release.arm64.so"

[dependencies]
ios.debug = {
    "res://bin/libgodot-cpp.ios.template_debug.xcframework": ""
}
ios.release = {
    "res://bin/libgodot-cpp.ios.template_release.xcframework": ""
}
```
<p>回到godot窗口,在左上角点"项目",点"重新加载当前项目",在左栏点"2D节点",右键"Node2D",点"添加子节点",在搜索栏键入GDExample,双击"匹配项"里的"GDExample"</p>
<p>左栏点击GDExample,使右栏显示出"Texture <空>"的一个项,在左栏点击icon.svg,鼠标拖动它到右栏<空>的地方,现在能在godot窗口正中间的显示区域看到什么东西动起来了</p>
<p>显示区域正上方点击一个手掌图案,可以用鼠标拖动显示区域</em></p>
