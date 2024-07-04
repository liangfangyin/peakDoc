# 一、简介

## 1、概况

WPF是一个桌面开发框架，它与分辨率无关的UI框架，使用基于矢量的呈现引擎，构建用于利用现代图形硬件。WPF 提供一套完善的应用程序开发功能，这些功能包括 Extensible Application Markup Language (XAML)、控件、数据绑定、布局、二维和三维图形、动画、样式、模板、文档、媒体、文本和版式。 WPF 属于 .NET，因此可以生成整合 .NET API 其他元素的应用程序。

## 2、文件框架

新建WPF应用程序，可以看到如下文件目录：

![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1666314989268-assets/web-upload/f68ef970-fae5-40ca-b314-c3cef2067fdd.png#alt=image-20221021091327187)

**App.xaml**

```xml
<Application x:Class="WpfApp1.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfApp1"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
         
    </Application.Resources>
</Application>
```

x:Class：表示关联的类

xmlns、xmlns:x：表示WPF标准的命名空间

xmlns:local：当前关联的本地目录空间，通常用于数据绑定等业务操作。

StartupUri：程序启动的初始页面

Application.Resources：资源目录，通常存放自定义样式或者引用的第三方样式。

**App.xaml.cs**

```c
namespace WpfApp1
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App : Application
    {
    }
}
```

这个文件就是上面x:Class关联的类，通常可以编写启动程序需要实现的功能，如读取配置、加载服务等

**MainWindow.xaml**

```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>

    </Grid>
</Window>
```

为WPF创建的默认页面，引用的命名空间和App.xaml意义差不多。

Title：当前页面的标题。

## 3、WPF规则

WPF布局原则

- 一个窗口中只能包含一个元素
- 不应显示设置元素尺寸
- 不应使用坐标设置元素的位置
- 可以嵌套布局容器

# 二、布局、控件

## 1、布局

**WPF布局容器**

- _StackPanel_: 水平或垂直排列元素、Orientation属性分别: Horizontal / Vertical
- _WrapPanel_ : 水平或垂直排列元素、针对剩余空间不足会进行换行或换列进行排列
- _DockPanel_ : 根据容器的边界、元素进行Dock.Top、Left、Right、Bottom设置
- _Grid_ : 类似table表格、可灵活设置行列并放置控件元素、比较常用
- _UniformGrid_ : 指定行和列的数量, 均分有限的容器空间
- _Canvas_ : 使用固定的坐标设置元素的位置、不具备锚定停靠等功能。

### 1.1、StackPanel

**StackPanel**：主要用于垂直或水平排列元素，在容器的可用尺寸内放置有限个元素，元素的尺寸总和(长/高)不允许超过StackPanel的尺寸, 否则超出的部分不可见。默认垂直显示。

```xml
<!--横向-->
        <StackPanel Orientation="Horizontal">
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button> 
        </StackPanel>
        <!--纵向-->
        <StackPanel Orientation="Vertical">
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
        </StackPanel>
```

### 1.2、WrapPanel

**WrapPanel**：默认横向方向显示，如果超过当前宽度尺寸，会自动换行显示。

```xml
<!--横向-->
        <WrapPanel>
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button> 
        </WrapPanel>
        <!--纵向-->
        <WrapPanel Orientation="Vertical">
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
            <Button Content="测试" Height="30"></Button>
        </WrapPanel>
```

### 1.3、DockPanel

**DockPanel**：支持让元素简单地停靠在整个面板的某一条边上，然后拉伸元素以填满全部宽度或高度。它也支持让一个元素填充其他已停靠元素没有占用的剩余空间。

DockPanel有一个Dock附加属性，因此子元素用4个值来控制她们的停靠：Left、Top、Right、Bottom。Dock 没有Fill值。作为替代，最后的子元素将加入一个DockPanel并填满所有剩余的空间，除非DockPanel的LastChildFill属性为false，它将朝某个方向停靠。

```xml
<DockPanel>
            <Button DockPanel.Dock="Top" Background="Aqua">1(Top)</Button>
            <Button DockPanel.Dock="Left" Background="Green">2(Left)</Button>
            <Button DockPanel.Dock="Right" Background="Yellow">3(Right)</Button>
            <Button DockPanel.Dock="Bottom" Background="Blue">4(Bottom)</Button>
            <Button Background="Orange">5</Button>
</DockPanel>
```

### 1.4、Grid

Grid：网格布局，可以自定义行和列并通过行列的数量、行高列宽来调整控件的布局。

```xml
//一行多列
<Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition></ColumnDefinition>
            <ColumnDefinition></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Button Grid.Column="0" Content="第1列"></Button>
        <Button Grid.Column="1" Content="第2列"></Button>
</Grid>

//多行多列
<Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition></ColumnDefinition>
            <ColumnDefinition></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition></RowDefinition>
            <RowDefinition></RowDefinition>
        </Grid.RowDefinitions>
        <Button Grid.Row="0" Grid.Column="0" Content="第1行，第1列"></Button>
        <Button Grid.Row="0" Grid.Column="1" Content="第1行，第2列"></Button>
        <Button Grid.Row="1" Grid.Column="0" Content="第2行，第1列"></Button>
        <Button Grid.Row="1" Grid.Column="1" Content="第2行，第2列"></Button>
</Grid>
```

### 1.5、UniformGrid

提供一种在网格(网格中的所有单元格都具有相同的大小)中排列内容的方法。

```xml
<UniformGrid Columns="3" Rows="3" FirstColumn="0">
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
            <Button Content="测试"></Button>
</UniformGrid>
```



### 1.6、Canvas

Canvas面板用于使用相对于画布区域的坐标来定位子元素。

1、Canvas 的 Height 和 Width 属性的默认值为 0。如果不设置这些值，除非子元素自动调整大小，否则您将看不到画布。

2、Canvas 上的子元素永远不会调整大小。

3、子元素上的垂直和水平对齐不起作用。子元素放置在由 Canvas Left、Top、Right 和 Bottom 属性设置的位置上。

4、如果设置了 Canvas 的 Left 属性，Right 属性将不起作用。如果设置了 Canvas 的 Top 属性，Bottom 属性将不起作用。

```xml
<Canvas Background="LightCyan" >
  <Rectangle Canvas.Left="10" Canvas.Top="10" Height="200" Width="200"  Stroke="Black" StrokeThickness="10" Fill="Red" />
  <Rectangle    Canvas.Left="60" Canvas.Top="60"   Height="200" Width="200"  Stroke="Black" StrokeThickness="10" Fill="Blue" />
  <Rectangle  Canvas.Left="110" Canvas.Top="110"    Height="200" Width="200" Stroke="Black" StrokeThickness="10" Fill="Green" />
</Canvas>
```

## 2、控件

### 2.1、控件容器介绍

![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1666314989295-assets/web-upload/479420f8-54a3-497d-849c-96798a7b941d.png#alt=image-20221021091442031)

控件主要类型有：ContentControl 、HeaderedContentControl 、ItemsControl 、Panel等几类。

**ContentControl 类**：设置内容时候如果是嵌套形式只能设置一个Content，可以使用Panel作为容器，实现多个控件显示。

```xml
<Button>
            <Button.Content>
                <WrapPanel>
                    <Label Content="测试"></Label>
                    <Label Content="测试"></Label>
                </WrapPanel>
            </Button.Content>
</Button>
```

**HeaderedContentControl 类**：这类控件即可设置Content, 还有带标题的Header。如GroupBox、TabControl子元素TabItem、它们都是具备标题和内容的控件。

```xml
<TabControl>
            <TabItem Header="测试1">
                <Button Content="测试1"></Button>
            </TabItem>
            <TabItem Header="测试2">
                <Button Content="测试2"></Button>
            </TabItem>
</TabControl>
```

**ItemsControl 类**：此类控件大多数属于显示列表类的数据、设置数据源的方式一般通过 ItemSource 设置。如ListBox。

```xml
<ListBox Margin="0"  
                         ItemsSource="{Binding MenuItems}"
                         SelectedItem="{Binding CurrentMenuItem}">
                    <ListBox.ItemTemplate>
                        <DataTemplate> 
                            <StackPanel HorizontalAlignment="Center"> 
                                <Grid Width="60px" Margin="0,3,0,3" Cursor="Hand">
                                    <TextBlock Text="{Binding Name}"></TextBlock>
                                </Grid>
                            </StackPanel>
                        </DataTemplate>
                    </ListBox.ItemTemplate> 
</ListBox>
```

### 2.2、基础控件

**GroupBox**：多选

```xml
<GroupBox Header="select number?">
       <StackPanel>
           <RadioButton>one</RadioButton>
           <RadioButton>two</RadioButton>
           <RadioButton>three</RadioButton>
       </StackPanel>
</GroupBox>
```

**TabControl**：标签页

```xml
<TabControl>
        <TabItem Header="首页">
            <StackPanel>
                <Button>button1</Button>
                <Button>button2</Button>
                <Button>button3</Button>
            </StackPanel>
        </TabItem>
        <TabItem Header="第二页">
            <StackPanel>
                <Button>button4</Button>
                <Button>button5</Button>
                <Button>button6</Button>
            </StackPanel>
        </TabItem>   
</TabControl>
```

与**Content**属性相同, TabItem的**Header**同样可以接收任何类型的对象，这样我们就可以自定义这部分内容。如下所示：

```xml
<TabControl>
        <TabItem >
            <TabItem.Header>
                <StackPanel Orientation="Horizontal">
                    <Button Background="Transparent" BorderBrush="Transparent">♥</Button>
                    <TextBox BorderBrush="Transparent">首页</TextBox>
                </StackPanel>
            </TabItem.Header>
            <StackPanel>
                <Button>button1</Button>
                <Button>button2</Button>
                <Button>button3</Button>
            </StackPanel>
        </TabItem>
    </TabControl>
```

**Expander**：菜单控件，具备标题的内容收缩控件

```xml
<StackPanel>
    <Expander Header="标题1">
        <TextBlock TextWrapping="Wrap">测试内容1.</TextBlock>
    </Expander>
    <Expander Header="标题2">
        <TextBlock TextWrapping="Wrap">测试内容2.</TextBlock>
    </Expander>
    <Expander Header="标题3">
        <TextBlock TextWrapping="Wrap">测试内容3.</TextBlock>
    </Expander>
</StackPanel>
```

**ListBox**：数据列表控件

```xml
<ListBox>
            <ListBoxItem>
                <Button>♥ button1</Button>
            </ListBoxItem>
            <ListBoxItem>
                <Button>♥ button2</Button>
            </ListBoxItem>
</ListBox>
```

**ProgressBar**：进度条

```xml
<ProgressBar Margin="2" IsIndeterminate="True" Height="30" Foreground="Green" ></ProgressBar>
```

# 三、数据绑定

数据绑定是在应用 UI 与其显示的数据之间建立连接的过程。 如果绑定具有正确的设置，并且数据提供适当的通知，则在数据更改其值时，绑定到该数据的元素会自动反映更改。

**绑定模式**：

		WPF有5中绑定模式，包含在System.Windows.Data.BindingMode枚举中。

| 标题 | 描述 |
| --- | --- |
| OneWay | 仅当源属性发生更改时更新目标属性。 |
| TwoWay | 无论是目标属性还是源属性，只要发生了更改，就会更新目标属性或属性。 |
| OneTime | 仅当应用程序启动或 [DataContext](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.datacontext) 进行更改时更新目标属性。 |
| OneWayToSource | 在目标属性更改时，更新源属性。 |
| Default | 导致使用目标属性的默认值 [Mode](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.data.binding.mode)。 |


```xml
<TextBlock Name="IncomeText" Text="{Binding Path=TotalIncome, Mode=OneTime}" />
```

**绑定错误**：

WPF不会引发异常来通知与数据绑定相关的问题, 如果指定的元素不存在或错误, 不会收到任何提示, 最简单的只是目标的属性无法发生改变, 不过, WPF仍然会在输出窗口显示其绑定失败的具体细节。

**绑定更新：**

从 源对象 -> 目标对象, 源对象的值发生改变, 目标会立刻响应。但是从目标 > 源 , 未必会立即发生。

| 名称 | 描述 |
| --- | --- |
| propertyChanged | 当目标属性发生变化时立即更新源目标 |
| LostFocus | 当目标属性发生变化时并且目标丢失焦点是更新源目标 |
| Explicit | 除非调用BindingExpression.UpdateSource()方法,否则无法更新资源 |
| Default | 根据目标属性的元数据确定更新行为, 大多数属性的默认行为是PropertyChanged, 但是TextBox.Text的属性默认行为是LoastFocus |


对于TextBox控件, 添加 UpdateSourceTrigger=PropertyChanged 以达到更新源目标, 但是往往在实际应用中, TextBox会频繁的变化, 从而导致多次更新源目标,

```xml
<StackPanel>
  <TextBlock Grid.Row="1" Text="测试" Name="txtfont"></TextBlock>
  <TextBox Grid.Row="2" Margin="10" Height="40" Text="{Binding ElementName=txtfont,Path=FontSize,UpdateSourceTrigger=PropertyChanged}"></TextBox>
</StackPanel>
```

### 1、控件相互绑定

#### **1.1、源发生更新，目标跟着变化**

```xml
<StackPanel>
            <Slider Name="s1Process" Value="10" Maximum="100" Margin="15"></Slider>
            <TextBlock FontSize="{Binding ElementName=s1Process,Path=Value}" Text="文字大小变化" Margin="15" ></TextBlock>
            <TextBox Text="{Binding ElementName=s1Process,Path=Value}" Margin="15" Height="40"></TextBox>
</StackPanel>
```

上述代码TextBlock的字体大小和TextBox的文本值，都是绑定Slider控制的value值的，当Slider控件值发生变化，下面两个控件也会跟着发生变化。

**Binding**：代表绑定数据源。

**ElementName**：代表绑定的控件

**Path**：绑定数据源的属性

#### **1.2、目标发生变化，源也发生变化**

```xml
<StackPanel>
  <TextBlock Grid.Row="1" Text="测试" Name="txtfont"></TextBlock>
  <TextBox Grid.Row="2" Margin="10" Height="40" Text="{Binding ElementName=txtfont,Path=FontSize,UpdateSourceTrigger=PropertyChanged}"></TextBox>
</StackPanel>
```

### 2、非元素绑定

上面我们说明的是数据源来源于控件，下面我们说明一下数据源来源非控件的元素属性，当绑定一个非元素对象时候, 这个时候就不能使用 ElementName属性, 在WPF中, 提供了多种属性以在实际应用中选择：

| 名称 | 描述 |
| --- | --- |
| Source | 提供数据的对象本身 |
| RelativeSource | 使用RelativeSource对象指向源目标, |
| DataContext | 从当前元素向下, 找到第一个非空的DataContext属性。 |


**Source**：

```xml
<Window.Resources>
  <FontFamily x:Key="Custom">微软雅黑</FontFamily>
</Window.Resources>
<Grid>
   <TextBlock  Text="{Binding Source={StaticResource Custom},Path=Source}" Grid.Row="1"  Name="txtfont" FontSize="15"></TextBlock>
</Grid>
```

**RelativeSource**:

```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="我是测试窗体" Height="450" Width="800">
    <Grid>
        <TextBlock Text="{Binding RelativeSource={RelativeSource Mode=FindAncestor,AncestorType={x:Type Window}},Path=Title}"/>
    </Grid>
</Window>
```

Mdoe=FindAncestor,通知元素发现AncestorType=window的元素，并且获取Path=title的属性值。

### 3、数据源绑定

数据源绑定通常用于MVVM模式，进行业务数据操作绑定，默认是双向绑定。

属性值变动类：

```c
 public class NotifyPropertyObject : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;

        protected void RaisePropertyChanged(string property)
        {
            try
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(property));
            }
            catch { }
        }

        protected virtual bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
        {
            try
            {
                if (EqualityComparer<T>.Default.Equals(storage, value))
                {
                    return false;
                }
                storage = value;
                RaisePropertyChanged(propertyName);
                return true;
            }
            catch {
                return false;
            }
        }
    }
```

#### 3.1、简单绑定

##### 3.1.1、变量值绑定：

当页面加载时候，所有从后端加载Name属性值到前端显示，当TextBox值发生变化，后端属性值也会跟着变化。

```xml
<UserControl x:Class="Cashier.Views.Product.Prod"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        xmlns:vm="clr-namespace:Cashier.ViewModels.Product" 
        d:DataContext="{d:DesignInstance Type={x:Type vm:ProdViewModel}}" >
  <Grid> 
     <TextBox Text="{Binding Name}"></TextBox>
   </Grid>
</UserControl>
```

```c
 public class ProdViewModel : ViewModelBase
 {
  	    private string name;
        public string Name
        {
            get => name;
            set
            {
                name = value;
                RaisePropertyChanged(nameof(Name));
            }
        }
 }
```

**xmlns:vm**：表示MVVM所关联的类在项目中的位置。

**d:DataContext**：表示当前页面关联的上下文的业务类。

**{Binding Name}**：表示绑定后端的属性名称。

##### 3.1.2、列表绑定

前端ListBox绑定后端数据源列表。

```xml
<ListBox ItemsSource="{Binding MenuItems}" SelectedItem="{Binding CurrentMenuItem}">
   <ListBox.ItemTemplate>
      <DataTemplate> 
         <TextBlock Text="{Binding Name}"></TextBlock>
      </DataTemplate>
   </ListBox.ItemTemplate>
</ListBox>
```

```c
 public class MainWindowViewModel : ViewModelBase
 {
  		private ObservableCollection<MenuItem> menuItems;
        public ObservableCollection<MenuItem> MenuItems
        {
            get => menuItems;
            set
            {
                menuItems = value;
                RaisePropertyChanged("MenuItems");
            }
        }

        private MenuItem currentMenuItem;
        public MenuItem CurrentMenuItem
        {
            get => currentMenuItem;
            set
            {
                currentMenuItem = value;
                RaisePropertyChanged("CurrentMenuItem");
            }
        }
 }
```

数据源绑定后端MenuItems，当选中ListBox其中一个选项，将自动将当前选中的数据赋值给与CurrentMenuItem。

# 三、样式

## 1、控件样式

```xml
 <Label Content="测试" FontSize="16" Foreground="Blue" FontWeight="Bold" ></Label>
```

直接将相关样式编写在控件上，这种最方便，但是如果内容过多就比较混乱。

## 2、页面资源样式

### 2.1、样式定义

页面资源样式，可以定义一个样式给与当前页面使用，通常可以用于多个相同样式的控件公用一个样式，如：列表下面的数据。

```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Title="我是测试窗体" Height="450" Width="800">
    <Window.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="FontSize" Value="15"></Setter>
        </Style>
    </Window.Resources>
    <Grid>
        <Button Content="测试" Style="{DynamicResource btnStyle}"></Button>
    </Grid>
</Window>
```

在页面定义资源Window.Resources，在资源下面就可以自定义样式。控件在引用样式时候分为两种：StaticResource和DynamicResource。

**StaticResource和DynamicResource之间的区别**

静态资源(Static Resource)，动态资源（Dynamic Resources）这两者的区别是：

**Static Resource**：静态资源在第一次编译后即确定其对象或值，之后不能对其进行修改。

**Dynamic Resources**：动态资源则是在运行时决定，当运行过程中真正需要时，才到资源目标中查找其值，因此，可以动态地修改它。由于动态资源的运行时才能确定其值，因此效率比静态资源要低。

DynamicResource用于换肤之类的功能，只要是双向绑定都使用动态资源。StaticResource和DynamicResource效果相同

### 2.2、样式触发器

#### 2.2.1、PropertyTrigger 属性触发器

根据属性的值设置属性值或启动操作的 [Trigger](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.trigger) 称为属性触发器。

```xml
<Window.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="FontSize" Value="15"></Setter>
            <Style.Triggers>
                <Trigger Property="IsMouseOver" Value="True"> 
                    <Setter Property="Foreground" Value="Orange"></Setter>
                </Trigger>
                <Trigger Property="IsEnabled" Value="True">
                    <Setter Property="Opacity" Value="0.6"></Setter>
                </Trigger>
            </Style.Triggers>
        </Style>
    </Window.Resources>
    <Grid>
        <Button Content="测试" Style="{StaticResource btnStyle}"></Button>
    </Grid>
```

1、鼠标滑动：当鼠标滑过的时候，按钮的颜色变成橘黄色

2、按钮禁用：当按钮被禁用的时候，按钮变成半透明色。

#### 2.2.2、EventTrigger 事件触发器

[EventTrigger](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.eventtrigger)，用于根据某个事件的发生启动一组操作。列表一个动画的触发

```xml
<Window.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="FontSize" Value="15"></Setter>
            <Style.Triggers>
                <Trigger Property="IsMouseOver" Value="True"> 
                    <Setter Property="Foreground" Value="Orange"></Setter>
                </Trigger>
                <Trigger Property="IsEnabled" Value="True">
                    <Setter Property="Opacity" Value="0.6"></Setter>
                </Trigger>
                <EventTrigger RoutedEvent="Mouse.MouseEnter">
                    <EventTrigger.Actions>
                        <BeginStoryboard>
                            <Storyboard>
                                <DoubleAnimation Duration="0:0:0.8" Storyboard.TargetProperty="Height" To="90"  />
                            </Storyboard>
                        </BeginStoryboard>
                    </EventTrigger.Actions>
                </EventTrigger>
                <EventTrigger RoutedEvent="Mouse.MouseLeave">
                    <EventTrigger.Actions>
                        <BeginStoryboard>
                            <Storyboard>
                                <DoubleAnimation Duration="0:0:1" Storyboard.TargetProperty="Height"  />
                            </Storyboard>
                        </BeginStoryboard>
                    </EventTrigger.Actions>
                </EventTrigger>
            </Style.Triggers>
        </Style>
    </Window.Resources>
    <Grid>
        <Button Content="测试" Style="{StaticResource btnStyle}"></Button>
    </Grid>
```

当鼠标滑到按钮上面时，利用0.8秒实现，高度从属性样式设置的40变成90，当鼠标移开，利用1秒恢复原来的1秒。

#### 2.2.3、MultiTrigger 多条件触发器

表示在满足一组条件时应用属性值或执行操作的触发器。

```xml
<Window.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="FontSize" Value="15"></Setter>
            <Style.Triggers>
                <MultiTrigger>
                    <MultiTrigger.Conditions>
                        <Condition Property="Height" Value="40" />
                        <Condition Property="Width" Value="120" />
                    </MultiTrigger.Conditions>
                    <Setter Property="MinWidth" Value="500"/>
                </MultiTrigger>
            </Style.Triggers>
        </Style>
    </Window.Resources>
    <Grid>
        <Button Content="测试" Style="{StaticResource btnStyle}"></Button>
    </Grid>
```

当满足Height=40 且 Width=120 时，按钮的最小宽度将变成500.

#### 2.2.4、DataTrigger 数据触发器

表示一个触发器，它会在绑定数据满足指定条件时应用属性值或执行操作，当符合条件的时候，将会触发。

```xml
<Window.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="FontSize" Value="15"></Setter>
            <Style.Triggers>
                <DataTrigger Binding="{Binding ElementName=texb, Path=Content}" Value="测试">
                    <Setter Property="Background" Value="Red"></Setter>
                </DataTrigger>
            </Style.Triggers>
        </Style>
    </Window.Resources>
    <Grid>
        <Button x:Name="texb" Content="测试" Style="{StaticResource btnStyle}"></Button>
    </Grid>
```

当name=texb控件的Content值等于测试时候，DataTrigger将会触发，设置背景颜色为红色。

#### 2.2.5、MultiDataTrigger 多条件数据触发器

表示一个触发器，它会在绑定数据满足一组条件时应用属性值或执行操作。

```xml
<Style TargetType="ListBoxItem">
    <Style.Triggers>
      <MultiDataTrigger>
        <MultiDataTrigger.Conditions>
          <Condition Binding="{Binding Path=Name}" Value="Portland" />
          <Condition Binding="{Binding Path=State}" Value="OR" />
        </MultiDataTrigger.Conditions>
        <Setter Property="Background" Value="Cyan" />
      </MultiDataTrigger>
    </Style.Triggers>
  </Style>
```

### 2.3、自定义外观

#### 2.3.1、ControlTemplate

在 WPF 中，控件的 [ControlTemplate](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.controls.controltemplate) 用于定义控件的外观。 可以通过定义新的 [ControlTemplate](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.controls.controltemplate) 并将其分配给控件来更改控件的结构和外观。每个控件都有一个分配给 [Control.Template](https://learn.microsoft.com/zh-cn/dotnet/api/system.windows.controls.control.template#system-windows-controls-control-template) 属性的默认模板。 该模板将控件的视觉呈现与控件的功能关联起来。通常在 XAML 文件的 `Resources` 部分中将模板声明为资源。 与所有资源一样，将应用范围规则。

```xml
<Window.Resources> 
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="Content" Value="测试"></Setter>
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="{x:Type Button}">
                        <Grid>
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="*"></ColumnDefinition> 
                            </Grid.ColumnDefinitions>
                            <Label Grid.Column="0" Content="哨兵"></Label>
                            <Button Grid.Column="1" Content="{TemplateBinding Content}" Height="{TemplateBinding Height}" Width="{TemplateBinding Width}"></Button>
                        </Grid>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style> 
    </Window.Resources>
    <Grid>
        <Button Style="{DynamicResource btnStyle}" Content="你好" Height="50" Width="200"></Button>
    </Grid>
```

**TemplateBinding**：绑定自定义传递的数据，这里Button传递Width自定义为200，如果不传将用默认值120.

### 2.5、自定义模板

1、新建WPF资源文件：

![](https://cdn.nlark.com/yuque/0/2022/png/2811620/1666314989324-assets/web-upload/2fe29c61-18d1-452b-8fc9-10b5dd5d0993.png#alt=image-20221021090154171)

2、编写资源文件

```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Style x:Key="btnStyle" TargetType="{x:Type Button}">
        <Setter Property="Height" Value="40"></Setter>
        <Setter Property="Width" Value="120"></Setter>
        <Setter Property="Content" Value="测试"></Setter>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type Button}">
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto"></ColumnDefinition>
                            <ColumnDefinition Width="*"></ColumnDefinition>
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="0" Content="哨兵"></Label>
                        <Button Grid.Column="1" Content="{TemplateBinding Content}" Height="{TemplateBinding Height}" Width="{TemplateBinding Width}"></Button>
                    </Grid>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```

3、打开App.xaml文件，并且应用新建的资源文件

```xml
<Application x:Class="WpfApp1.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfApp1"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/WpfApp1;component/Dictionary1.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

4、页面引用样式

```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"  BorderThickness="2"
        Title="MainWindow" Height="450" Width="800"> 
    <Grid>
        <Button Style="{DynamicResource btnStyle}" Content="你好" Height="50" Width="200"></Button>
    </Grid>
</Window>
```

### 2.6、公共样式

公共样式与"自定义模板"类似，不过公共样式是编写在App.xmal文件中编写，可以整个项目所有地方使用。

1、打开App.xmal文件，新增如下样式。

```xml
<Application x:Class="WpfApp1.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfApp1"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <Style x:Key="btnStyle" TargetType="{x:Type Button}">
            <Setter Property="Height" Value="40"></Setter>
            <Setter Property="Width" Value="120"></Setter>
            <Setter Property="Content" Value="测试"></Setter>
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="{x:Type Button}">
                        <Grid>
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="Auto"></ColumnDefinition>
                                <ColumnDefinition Width="*"></ColumnDefinition>
                            </Grid.ColumnDefinitions>
                            <Label Grid.Column="0" Content="哨兵"></Label>
                            <Button Grid.Column="1" Content="{TemplateBinding Content}" Height="{TemplateBinding Height}" Width="{TemplateBinding Width}"></Button>
                        </Grid>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
    </Application.Resources>
</Application>
```

2、页面引用样式

```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"  BorderThickness="2"
        Title="MainWindow" Height="450" Width="800"> 
    <Grid>
        <Button Style="{DynamicResource btnStyle}" Content="你好" Height="50" Width="200"></Button>
    </Grid>
</Window>
```

# 四、事件

### 1、页面事件

### 2、MVVM事件

### 3、命令

未完待续.....
