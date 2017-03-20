Doxygen
================================================================================================================
## Doxygen简介
   doxygen注释块就是在C/C++注释块的基础上添加的一些额外的标识，使得doxygen把它识别出来，并将它组织到生成的文档中。
## Doxygen注释
   doxygen支持的注释风格：
   1. javaDoc风格 
      ```
      /**
       *......
       *......
       *......
       */
       ```
   2. QT风格
      ```
      /*!
      ....
      ....
      */
      ```
   3. C/C++行注释风格
      //或//!
## Doxygen关键字
   在doxygen文档中有很多@前缀的语句。
   @file 文件名字
   @date 文件生成日期
   @oaram 参数
## Doxygen插件
  在vim环境中为了简化doxygen注释的编写，可以使用doxygenTooKit.vim来实现
  注释的自动化加入。
### 安装Doxygen
   1. 将doxygentoolkit.vim复制到.vim/plugin中
   2. 配置.vimrc设置全局变量(全局设置在/etc/vimrc)
      ```
      map fa : DoxAuth<cr><cr>
      map fg : Dox<cr>
      map fl : DoxLic<cr>
      #设置全局变量
      let g:DoxygenToolkit_authorName=$USER
      let g:DoxygenToolkit_licenseTag="licese<cr>"
      let g:DoxygenToolkit_briefTag_pre="@brief\t"
      let g:DoxygenToolkit_paramTag_pre="@param\t"
      let g:DoxygenToolkit_returnTag="@return\t"
      let g:DoxygenToolkit_maxFunctionProtoLines=30
      ```
   3. 指定使用的插件：source XXXX/DoxygenToolkit.vim
## 使用Doxygen生成说明文档
   1. 系统中安装doxygen
   2. 生成配置文件
      ```
      doxygen -g conf_name
      ```
   3. 配置doxygen文件
      - EXTRACT_ALL 表示输出所有函数，但是private和static函数不属于其管制
      - EXTRACT_PRIVATE 输出private函数
      - EXTRACT_STATIC 输出static函数
      - EXTRACT_LOCAL_CLASSES YES包含源文件和头文件中的定义， NO只包含在头文件中定义的
      - INPUT 文档输入目录
      - OUTPUT 文档输出目录
      - RECURSIVE 是否子目录生效
      - EXAMPLE_PATTERNS = *.cpp/*.h 过滤目录中文件类型进行文档生成
      - GENERATE_LATEX =YES 生成Latex输出, NO不输出
