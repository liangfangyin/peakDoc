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













