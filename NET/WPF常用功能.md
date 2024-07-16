# 窗口设置

## 居中

```xml
<Window x:Class="Music.MainWindow" WindowStartupLocation="CenterScreen">
```



## 拖动

```xml
<Window x:Class="Music.MainWindow" MouseMove="Window_MouseMove">
```

```c#
private void Window_MouseMove(object sender, MouseEventArgs e)
{
    try
    {
        this.DragMove();
    }
    catch { }
}
```



## 去除默认窗口

```xml
<WindowChrome.WindowChrome>
     <WindowChrome GlassFrameThickness="0"></WindowChrome>
</WindowChrome.WindowChrome>
```



## 去除窗口样式

```xml
<Window WindowStyle="None">
```



## 引用资源

```xml
<Window.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="/Themes/MainWindowStyle.xaml"></ResourceDictionary>
            <ResourceDictionary Source="pack://application:,,,/PeakUI.WPF;component/Themes/Themes.xaml"></ResourceDictionary>
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>  
</Window.Resources>
```



## 全局引用资源

App.xaml 文件

```xml
<Application x:Class="Music.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:Music"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <!--主题颜色-->
                <ResourceDictionary Source="/Themes/Style/BlackColor.xaml"></ResourceDictionary>
                <ResourceDictionary Source="/Themes/Style/DefaultColor.xaml"></ResourceDictionary>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary> 
    </Application.Resources>
</Application>

```



## 引入字体图标

字体文件/Assets/Fonts/iconfont.ttf

```xml
<Application x:Class="Music.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:Music"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <!-- 定义全局字体资源 -->
            <FontFamily x:Key="IconFontFamily">/Assets/Fonts/#iconfont</FontFamily>
            <!-- 定义样式用于方便使用字体图标 -->
            <Style TargetType="TextBlock" x:Key="IconTextBlockStyle">
                <Setter Property="FontFamily" Value="{StaticResource IconFontFamily}"/>
            </Style>
            <Style TargetType="Button" x:Key="IconButtonStyle">
                <Setter Property="FontFamily" Value="{StaticResource IconFontFamily}"/>
            </Style>
        </ResourceDictionary> 
    </Application.Resources>
</Application>
```

使用

```xml
<TextBlock Style="{DynamicResource IconTextBlockStyle}" Text="&#xe73b;"></TextBlock>
```



## 资源引入图片

资源文件DefaultColor.xaml

```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <!--主要-->
    <BitmapImage x:Key="Logo" UriSource="/Assets/logo1.png"></BitmapImage>

    <SolidColorBrush x:Key="MainBackColor" Color="#ec4141"/> 
</ResourceDictionary>
```



## 嵌入页面

前端页面

```xml
<Frame  Name="navUrl"></Frame>
```

后台代码

```c#
 navUrl.Source = new Uri("/Contrils/Page1.xaml",UriKind.Relative);
```

Page1.xaml

```xml
<Page x:Class="Music.Contrils.Page1" >
    <Grid>
        <TextBlock Text="1111111111111111" FontSize="22"></TextBlock>
    </Grid>
</Page>

```



## 页面背景图

```xml
<Window.Background>
    <ImageBrush ImageSource="/Resources/Images/loginbg.jpg"></ImageBrush>
</Window.Background>
```



## APP异常

```c#

    public partial class App : Application
    {
        public App()
        {
            AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException;
            DispatcherUnhandledException += App_DispatcherUnhandledException;
            TaskScheduler.UnobservedTaskException += TaskScheduler_UnobservedTaskException;  
        }

        protected override void OnStartup(StartupEventArgs e)
        {
            base.OnStartup(e);
            Login indexView = new Login();
            indexView.Show();
        }
         
        private void App_DispatcherUnhandledException(object sender, DispatcherUnhandledExceptionEventArgs e)
        {
            PeakLog.ERROR(e.Exception.ToString(),nameof(App_DispatcherUnhandledException));
            e.Handled = true;  
        }

        private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e)
        {
            Exception ex = (Exception)e.ExceptionObject;
            PeakLog.ERROR(ex.Message + ex.StackTrace, nameof(CurrentDomain_UnhandledException)); 
        }

        /// <summary>
        /// Task线程内未捕获异常处理事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void TaskScheduler_UnobservedTaskException(object? sender, UnobservedTaskExceptionEventArgs ex)
        {
            PeakLog.ERROR(ex.Exception.Message + ex.Exception.StackTrace, nameof(TaskScheduler_UnobservedTaskException));
            //设置该异常已察觉（这样处理后就不会引起程序崩溃）
            ex.SetObserved();
        }
    }

```



## 依赖注入

```c#
public partial class App : Application
{
    public App()
    {
        Task.Run(() =>
        {
            var builder = new ConfigurationBuilder();
            builder.AddJsonFile(path: "appsettings.json", optional: true, reloadOnChange: true);
            ConfigurationUtil.Configuration = builder.Build();
             
            var serviceCollection = new ServiceCollection();
            serviceCollection.AddDistributedMemoryCache();
            serviceCollection.AddTransient<ISqlDbContext, SqlSugarDBClient>();
            serviceCollection.BuildServiceProvider();
            SelectItemValue.ServiceProvider = serviceCollection.BuildServiceProvider();
        }); 
 }
```





# 控件

## 阴影与剪裁

```xml
<Border Width="385" Height="535">
	阴影
    <Border.Effect>
         <DropShadowEffect Color="#2196F3" ShadowDepth="0" BlurRadius="12" Opacity="0.3" Direction="0"/>
    </Border.Effect>
    剪裁
    <Border.Clip>
        <RectangleGeometry RadiusX="{Binding CornerRadius.TopLeft, RelativeSource={RelativeSource AncestorType=Border}}"
        RadiusY="{Binding CornerRadius.TopLeft, RelativeSource={RelativeSource AncestorType=Border}}" Rect="0,0,385,535"/>
    </Border.Clip>
</Border>
```



### 图像剪裁圆角

```xml
 <Image Source="/Resources/Images/logo.png" Width="90px" Height="90px">
     <Image.Clip>
         <RectangleGeometry RadiusX="90" RadiusY="90" Rect="0,0,90,90" />
     </Image.Clip>
 </Image>
```



## 自定义控件

### 控件绘制

**绘制控件内容**

```xml
<Style x:Key="HeaderSearchLeft" TargetType="{x:Type Button}">
    <Setter Property="Height" Value="37"></Setter>
    <Setter Property="Width" Value="25"></Setter>
    <Setter Property="BorderThickness" Value="0"></Setter>
    <Setter Property="Opacity" Value="0.8"></Setter>
    <Setter Property="Cursor" Value="Hand"></Setter>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate>
                <Border CornerRadius="5" BorderThickness="1" Height="32" Width="23">
                    <Image Source="/Assets/Images/ljian.png" Width="15"></Image>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

### 控件事件样式

**绘制事件样式**

```xml
<Style x:Key="HeaderSearchLeft" TargetType="{x:Type Button}">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate>
                <Border CornerRadius="5" BorderThickness="1">
                    <Image Source="/Assets/Images/ljian.png" Width="15"></Image>
                </Border>
                <ControlTemplate.Triggers>
                    <Trigger Property="IsMouseOver" Value="True">
                        <Setter Property="Opacity" Value="1"></Setter>
                    </Trigger>
                </ControlTemplate.Triggers>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

**样式事件样式**

```xml
<Style x:Key="HeaderUserTouVip" TargetType="{x:Type Image}">
    <Setter Property="Height" Value="21"></Setter>
    <Setter Property="Width" Value="57"></Setter>
    <Setter Property="Margin" Value="5,-2,0,0"></Setter>
    <Setter Property="Opacity" Value="0.9"></Setter>
    <Setter Property="Cursor" Value="Hand"></Setter>
    <Style.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter Property="Opacity" Value="1"></Setter>
        </Trigger>
    </Style.Triggers>
</Style>
```



## 数据绑定

### MVVM绑定

1、前端页面

```c#
 public partial class MainWindow : Window
 {
     public MainWindow()
     {
         InitializeComponent();
         //实体
         DataContext = new MainWindowViewModel();
     }
 }
```

2、功能

```C#
namespace Cashier.ViewModels
{
    public class MainWindowViewModel : NotifyPropertyObject
    {
         private CateAddModel cateAddModel=new CateAddModel();
         public CateAddModel CateAddModel
         {
             get => cateAddModel;
             set
             {
                 cateAddModel = value;
                 RaisePropertyChanged("CateAddModel");
             }
         }

        #region 事件
            
        /// <summary>
        /// 退出
        /// </summary>
        public ICommand LoginOutDialog => new RelayCommand((object sender) =>
        {
            ApplicationWindow.CloseApplication();
        });
         
        #endregion

    }
}
```

3、绑定

```c#
using System.Collections.Generic;
using System.ComponentModel;
using System.Runtime.CompilerServices;

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
         catch
         {
             return false;
         }
     }
 }

 public class RelayCommand : ICommand
 {
     private readonly Action<object> _execute;
     private readonly Func<object, bool> _canExecute;

     public RelayCommand(Action<object> execute) : this(execute, null) { }

     public RelayCommand(Action<object> execute, Func<object, bool> canExecute)
     {
         _execute = execute ?? throw new ArgumentNullException(nameof(execute));
         _canExecute = canExecute ?? (x => true);
     }

     public bool CanExecute(object parameter)
     {
         return _canExecute(parameter);
     }

     public void Execute(object parameter)
     {
         _execute(parameter);
     }

     public event EventHandler CanExecuteChanged
     {
         add
         {
             CommandManager.RequerySuggested += value;
         }
         remove
         {
             CommandManager.RequerySuggested -= value;
         }
     }

     public void Refresh()
     {
         CommandManager.InvalidateRequerySuggested();
     }
 }
```





### 文本绑定

```xml
<TextBox Text="{Binding LoginModel.UserName,Mode=TwoWay}" ></TextBox>
<CheckBox IsChecked="{Binding IsCheckBox}" Content="{Binding Name}"></CheckBox> 
<Label Content="{Binding Path=Lastlogindate}" ContentStringFormat="{}{0:yyyy-MM-dd HH:mm:ss}"></Label>
```



### ComboBox列表绑定

```xml
<ComboBox ItemsSource="{Binding StaffAddModel.Industry,Mode=TwoWay}" SelectedItem="{Binding StaffAddModel.SelectIndustry,Mode=TwoWay}" DisplayMemberPath="Text" SelectedValuePath="Value" />
```



### ListBox列表绑定

```xml
 <ListBox  
                  ItemsSource="{Binding MainWindowModel.MenuItems,Mode=TwoWay}"
                  ScrollViewer.VerticalScrollBarVisibility="Hidden"
                  ScrollViewer.HorizontalScrollBarVisibility="Hidden"
                  SelectedItem="{Binding MainWindowModel.CurrentMenuItem,Mode=TwoWay}">
     <ListBox.ItemTemplate>
         <DataTemplate>
             <StackPanel HorizontalAlignment="Center" Cursor="Hand" Width="60px" >
                 <Grid>
                     <peak:Icon Type="{Binding Ico}" />
                 </Grid>
                 <Grid>
                     <TextBlock Text="{Binding Name}"></TextBlock>
                 </Grid>
             </StackPanel>
         </DataTemplate>
     </ListBox.ItemTemplate>
 </ListBox>
```



### DataGrid列表绑定

前端：

```xml
<DataGrid Name="gridProduct" ItemsSource="{Binding StaffModel.ListStaff,Mode=TwoWay}"> 
    <DataGrid.Columns>
        <DataGridTextColumn Width="*" Binding="{Binding Name}" Header="用户名"/>
        <DataGridTextColumn Width="*" Binding="{Binding Staffname}" Header="姓名"/>
        <DataGridTextColumn Width="*" Binding="{Binding RoleName}" Header="角色"/>
        <DataGridTemplateColumn Header="登录时间" Width="*">
            <DataGridTemplateColumn.CellTemplate>
                <DataTemplate>
                    <WrapPanel>
                        <Label Content="{Binding Path=Lastlogindate}"  ContentStringFormat="{}{0:yyyy-MM-dd HH:mm:ss}" Style="{DynamicResource DataColumn}" ></Label>
                    </WrapPanel>
                </DataTemplate>
            </DataGridTemplateColumn.CellTemplate>
        </DataGridTemplateColumn>
        <DataGridTemplateColumn Header="操作" Width="185">
            <DataGridTemplateColumn.CellTemplate>
                <DataTemplate>
                    <WrapPanel Visibility="{Binding IsCheck}">
                        <Button Name="btnEdit"  Command="{Binding DataContext.EditUser, RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=DataGrid}}"
                         CommandParameter="{Binding RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=DataGrid}, Path=SelectedItem}"  Content="编辑"></Button>
                        <Button Name="btnReset" Command="{Binding DataContext.ResetPass, RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=DataGrid}}" 
                         CommandParameter="{Binding RelativeSource={RelativeSource Mode=FindAncestor, AncestorType=DataGrid}, Path=SelectedItem}"  Content="重置密码"></Button>
                    </WrapPanel>
                </DataTemplate>
            </DataGridTemplateColumn.CellTemplate>
        </DataGridTemplateColumn>
    </DataGrid.Columns>
</DataGrid>
```

后端事件：

```c#
        /// <summary>
        /// 重置密码
        /// </summary>
        /// <returns></returns>
        public RelayCommand ResetPass => new((object parame) =>
        {
            staffView.ResetPassword((SysUsersResponse)parame);
        });

        /// <summary>
        /// 编辑用户
        /// </summary>
        public RelayCommand EditUser => new((object parame) =>
        {
            staffView.EditUser(StaffModel, (SysUsersResponse)parame);
        });
```

选中事件

```c#
 private void datagrid_SelectionChanged(object sender, SelectionChangedEventArgs e)
 {
     DataGrid dataGrid = sender as DataGrid;
     if (dataGrid == null) return;
     Person person = dataGrid.SelectedItem as Person;
     if (person == null) return;
     _text1.Text = person.Name;
     _text2.Text = person.Age.ToString();
     _text3.Text = person.Address;
 }
```



### 控件对象数据绑定

```xml
<TextBlock x:Name="TXT" Text="5555"/>
<Button x:Name="btn" Content="{Binding ElementName=TXT, Path=Text,Mode=OneWay}"></Button>
```



### 控件后台自动更新

```xml
<TextBox Text="{Binding UserName,UpdateSourceTrigger=PropertyChanged,Mode=TwoWay}"/>
```

 UpdateSourceTrigger的作用是：当做何种改变的时候通知数据源我们做了改变。该属性有如下表格的四种值，详细见下面表格的描述。

| 枚举类型        | 效果                                   |
| --------------- | -------------------------------------- |
| Default         | 默认值（默认为LostFocuse）             |
| Explicit        | 当应用程序调用 UpdateSource 方法时生效 |
| LostFocus       | 失去焦点的时候触发                     |
| PropertyChanged | 数据属性改变的时候触发                 |



### 页面加载绑定

```xml
<ContentControl Content="{Binding MainWindowModel.CurrentMenuItem.Content,Mode=TwoWay}" />
```



### 按钮事件

```xml
<Button Command="{Binding LoginOutDialog}"></Button>
<Button Command="{Binding CloseControl}" CommandParameter="{Binding RelativeSource={RelativeSource FindAncestor, AncestorType=Window}}" Content="取消" />
<Button Command="{Binding SavePayment}" CommandParameter="{Binding RelativeSource={RelativeSource FindAncestor, AncestorType=UserControl}}" Content="保存"/>
```

```c#
// <summary>
/// 退出
/// </summary>
public ICommand LoginOutDialog => new RelayCommand((object sender) =>
{
    ApplicationWindow.CloseApplication();
});
```



## 数据类型转换

前端页面：

```xml
<Window x:Class="Cashier.Views.Cashiers.Shop"
        xmlns:converter="clr-namespace:Cashier.Core.Converter" > 
    <Window.Resources>
        <converter:PackToVisibilityConverter x:Key="PackToVisibilityConverter"/>
   </Window.Resources>
   
   <Border Visibility="{Binding PackId,Converter={StaticResource PackToVisibilityConverter},ConverterParameter=0}">
    <TextBlock Text="{Binding PackName}"></TextBlock>
  </Border> 
</Window>   
```

后端代码：

```c#
public class PackToVisibilityConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    { 
        if ((long)value>0)
        {
            return Visibility.Visible;
        }
        return Visibility.Collapsed;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        bool isChecked = (bool)value;
        if (!isChecked)
        {
            return null;
        }
        return Visibility.Visible;
    }
}
```



## 布局控件

### WrapPanel

- 自动换行：当子控件无法在当前行放下时，它们会自动移到下一行。

- 布局方向：可以设置为水平（Horizontal）或垂直（Vertical）排列。

  | 类型                 | 描述                                       |
  | -------------------- | ------------------------------------------ |
  | Orientation          | 指定子内容的排列方向，默认是按水平方向排列 |
  | Background           | 背景色                                     |
  | Width/Height         | 宽度和高度                                 |
  | ItemHeight/ItemWidth | WrapPanel 中所含全部项的高度/宽度          |

```xml
<WrapPanel ItemWidth="80" ItemHeight="50" Margin="10,10,10,10" Orientation="Vertical">
    <Button Content="1" FontSize="25" Margin="5" HorizontalAlignment="Right"/>
    <Button Content="2" FontSize="25" Margin="5"/>
    <Button Content="3" FontSize="25" Margin="5"/>
    <Button Content="4" FontSize="25" Margin="5"/>
    <Button Content="5" FontSize="25" Margin="5"/>
    <Button Content="6" FontSize="25" Margin="5"/>
    <Button Content="7" FontSize="25" Margin="5"/>
    <Button Content="8" FontSize="25" Margin="5"/>
    <Button Content="8" FontSize="25" Margin="5"/>
    <Button Content="8" FontSize="25" Margin="5"/>
    <Button Content="8" FontSize="25" Margin="5"/>
    <Button Content="8" FontSize="25" Margin="5"/>
</WrapPanel>
```



### UniformGrid

UniformGird中同一行中，列等宽，同一列中，行等高。常用属性为：

Margin：获取或设置元素的外边距。
Name：元素的标识名称；
Opacity：透明度
Width/Height：宽度和高度；
Visibility：该元素可见性；
Rows：获取或设置网格中的行数；
Columns：获取或设置网格中的列数；
FirstColumn    获取或设置网格第一行中前导空白单元格的数量，必须小于属性的值 Columns；

```xml
<UniformGrid FirstColumn="1" Columns="4" Margin="5">
    <Button Content="按钮1" Margin="2"></Button>
    <Button Content="按钮2" Margin="2"></Button>
    <Button Content="按钮3" Margin="2"></Button>
    <Button Content="按钮4" Margin="2"></Button>
    <Button Content="按钮5" Margin="2"></Button>
    <Button Content="按钮6" Margin="2"></Button>
</UniformGrid>
```



### ItemsControl

**ItemsControl条目控件，用于显示数据项集合，它允许按照自定义方式呈现任何类型的对象**，可以在其中使用不同的布局和面板来展示数据。

ItemsControl的常用知识点如下：

ItemTemplate，是DataTemplate类型，可以通过ItemTemplate设置条目项的呈现方式。
ItemsPanel，是ItemsPanelTemplate类型，可以通过ItemsPanel设置容器中各个条目项的布局方式。
ItemsSource，可以通过绑定数据源为ItemsControl设置内容。
Items，条目列表，如果设置了ItemsSource，则此属性不生效。

```xml
<ItemsControl ItemsSource="{Binding Customers}">
    <ItemsControl.ItemTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding Name}" />
        </DataTemplate>
    </ItemsControl.ItemTemplate>
    <ItemsControl.ItemsPanel>
        <ItemsPanelTemplate>
            <StackPanel />
        </ItemsPanelTemplate>
    </ItemsControl.ItemsPanel>
</ItemsControl>
```

```xml
<ScrollViewer VerticalScrollBarVisibility="Auto">
    <ItemsControl ItemsSource="{Binding ImageItems}" Background="#eeeeee">
        <ItemsControl.ItemsPanel>
            <ItemsPanelTemplate>
                <UniformGrid Columns="5"></UniformGrid>
            </ItemsPanelTemplate>
        </ItemsControl.ItemsPanel>
        <ItemsControl.ItemTemplate>
            <DataTemplate>
                <StackPanel Orientation="Vertical" Margin="3">
                    <Image Source="{Binding ImagePath}" Stretch="Uniform"></Image>
                    <TextBlock Text="{Binding ImageName}"></TextBlock>
                </StackPanel>
            </DataTemplate>
        </ItemsControl.ItemTemplate>
    </ItemsControl>
</ScrollViewer>
```



### Popup弹出框

Popup 控件提供一种在单独窗口中显示内容的方法，该窗口相对于指定元素或屏幕坐标在当前应用程序窗口上浮动。  Popup控件通过IsOpen属性控制是否可见。 当可见时，IsOpen 属性设置为 true，否则为 false。

IsOpen：布尔值,指示 Popup 控件是否显示
StaysOpen：布尔值,指示在 Popup 控件失去焦点的时候,是否关闭 Popup 控件的显示
PopupAnimation：指示显示窗口时是否使用动画,只有在 AllowsTransparency 等于true时此属性才有用
Popup： 窗口本身是一个不可见的元素,只有在窗口上放置了信息后才能显示
Popup的定位方式与一般控件的定位方法不一样, Popup 使用五个属性来设定位置信息:
PlacementTarget：设定 Popup 定义所相对的控件,如果没有为属性为 NULL,则 Popup 定位相对于屏幕的左上角
Placement：一个枚举值,指定 Popup 控件的定位方式
PlacementRectangle：设定一个矩形,在 Popup 控件显示时,位置将相对于此矩形来显示,此矩形的位置也相对于PlacementTarget 属性所设定的控件
HorizontalOffset：指定一个值,指示 Popup 的位置所需水平移动多少个象素
VerticalOffset：指定一个值,指示 Popup 的位置所需垂直移动多少个象素

```xml

<CheckBox x:Name="myCheckBox"></CheckBox>
<Popup Grid.Column="0" IsOpen="{Binding ElementName=myCheckBox,Path=IsChecked}" PlacementTarget="{Binding ElementName=myCheckBox}" AllowsTransparency="True" PopupAnimation="Slide"  StaysOpen="False">
	<Canvas Width="100" Height="100" Background="DarkBlue">
		<TextBlock TextWrapping="Wrap" Foreground="White" Text="旋转Popup" ></TextBlock>
	</Canvas>
</Popup>
```



### Polygon多边形

Points：定义多边形各个顶点的坐标。每个坐标使用空格或逗号分隔，多个坐标之间使用空格分隔。

Fill：定义多边形的填充颜色。可以使用颜色名称、十六进制值或RGB值来指定颜色。

Stroke：定义多边形的边界颜色。

StrokeThickness：定义多边形的边界宽度。

```xml
<Polygon Points="5,5 200,5 5,300 200,300" StrokeThickness="20" Margin="20">
    <Polygon.Stroke>
        <LinearGradientBrush>
            <GradientStop Color="DodgerBlue" Offset="1"/>
            <GradientStop Color="GreenYellow" Offset="0.5"/>
            <GradientStop Color="MediumSeaGreen" Offset="0"/>
        </LinearGradientBrush>
    </Polygon.Stroke>
</Polygon>
```

动画

```xml
<Polygon Name="myPolygon" VerticalAlignment="Center" Points="0,0 100,0 100,50 0,100" Fill="GreenYellow" Stroke="Black">
    <Polygon.Triggers>
        <EventTrigger RoutedEvent="Loaded">
            <BeginStoryboard>
                <Storyboard>
                    <DoubleAnimation From="0" To="200" Duration="0:0:2" Storyboard.TargetName="myPolygon" Storyboard.TargetProperty="(UIElement.RenderTransform).(TranslateTransform.X)"/>
                </Storyboard>
            </BeginStoryboard>
        </EventTrigger>
    </Polygon.Triggers>
    <Polygon.RenderTransform>
        <TranslateTransform X="0" Y="0"/>
    </Polygon.RenderTransform>
</Polygon>
```





