## 框架详解

[TOC]



#### 类图

```mermaid
classDiagram

class VulkanExampleBase{
	-VkPhysicalDevice physicalDevice;
	-VkPhysicalDeviceProperties deviceProperties;
	-VkPhysicalDeviceFeatures deviceFeatures;
	-VkPhysicalDeviceMemoryProperties deviceMemoryProperties;
	-VkPhysicalDeviceFeatures enabledFeatures;
	
	-VkDevice device;
	-VkQueue queue;
	-VkFormat depthFormat;
	-VkCommandPool cmdPool;
	-VkPipelineStageFlags submitPipelineStages;
	-VkSubmitInfo submitInfo;
	-std::vector~VkCommandBuffer~ drawCmdBuffers;
	-std::vector~VkFramebuffer~frameBuffers;
	-uint32_t currentBuffer = 0;
	
	-std::vector~VkShaderModule~ shaderModules;
	-VkPipelineCache pipelineCache;
	
	-VulkanSwapChain swapChain;
	
	-std::vector~VkFence~ waitFences;
	
	-uint32_t width = 1280;
	-uint32_t height = 720;
	+VulkanDevice *vulkanDevice;
	+ Camera camera;
	
	+HWND window;
	+HINSTANCE windowInstance;	
}

class VulkanSwapChain{
	- VkInstance instance;
	- VkDevice device;
	- VkPhysicalDevice physicalDevice;
	- VkSurfaceKHR surface;
	
	+VkFormat colorFormat;
	+VkColorSpaceKHR colorSpace;
	+VkSwapchainKHR swapChain;
	
	+std::vector~VkImage~ images;
	+std::vector~SwapChainBuffer~ buffers;
	+uint32_t queueNodeIndex = UINT32_MAX;	
	
	+void initSurface(void* platformHandle, void* platformWindow);
	
	+void connect(VkInstance instance, VkPhysicalDevice physicalDevice, VkDevice device);
	+void create(uint32_t* width, uint32_t* height, bool vsync = false, bool fullscreen = false);
	+VkResult acquireNextImage(VkSemaphore presentCompleteSemaphore, uint32_t* imageIndex);
	+VkResult queuePresent(VkQueue queue, uint32_t imageIndex, VkSemaphore waitSemaphore = VK_NULL_HANDLE);
	+void cleanup();	
}

class VulkanDevice{
	+VkPhysicalDevice physicalDevice;
	+VkDevice logicalDevice;
	+VkPhysicalDeviceProperties properties;
	+VkPhysicalDeviceFeatures features;
	+VkPhysicalDeviceFeatures enabledFeatures;
	+VkPhysicalDeviceMemoryProperties memoryProperties;
	+std::vector~VkQueueFamilyProperties> queueFamilyProperties;
	+std::vector~std::string> supportedExtensions;
	+VkCommandPool commandPool = VK_NULL_HANDLE;
}

class Texture{
	vks::VulkanDevice *   device;
	VkImage               image;
	VkImageLayout         imageLayout;
	VkDeviceMemory        deviceMemory;
	VkImageView           view;
	uint32_t              width, height;
	uint32_t              mipLevels;
	uint32_t              layerCount;
	VkDescriptorImageInfo descriptor;
	VkSampler             sampler;	
}

VulkanExampleBase o-- VulkanSwapChain
VulkanExampleBase o-- VulkanDevice
```

#### 流程

```c++
int main
{
    // 处理commandline, console等信息
    vulkanExample = new VulkanExample();
    
    vulkanExample->initVulkan();
    {
        VkResult VulkanExampleBase::createInstance(bool enableValidation);
        {
            // 获取支持的Extension
         	// 获取已经启用的Extension
            //校验层设置
         
            //创建VkInstance
            vkCreateInstance(&instanceCreateInfo, nullptr, &instance);
        }
        
        // 获取GPU，封装为VkPhysicalDevice，使用第一个作为程序使用的VkPhysicalDevice
        std::vector<VkPhysicalDevice> physicalDevices(gpuCount);
        err = vkEnumeratePhysicalDevices(instance, &gpuCount, physicalDevices.data());
        physicalDevice = physicalDevices[selectedDevice];
        
        // 
        vulkanDevice = new vks::VulkanDevice(physicalDevice);
        {
            // 获取GPU的属性，限制，内存信息等等

            // 获取队列簇属性
            
            // 获取支持的扩展
        }
        
        // 创建逻辑设备
        VkResult res = vulkanDevice->createLogicalDevice(enabledFeatures, enabledDeviceExtensions, deviceCreatepNextChain);
        {
            // 填写VkDeviceQueueCreateInfo结构体，指定要创建的队列类型和其他信息
            
            // 添加使用swapChain的扩展
            
            // 创建VkDevice，逻辑设备
            VkResult result = vkCreateDevice(physicalDevice, &deviceCreateInfo, nullptr, &logicalDevice);
            //创建默认的commandPool
            commandPool = createCommandPool(queueFamilyIndices.graphics);
        }
        
            // Get a graphics queue from the device
        vkGetDeviceQueue(device, vulkanDevice->queueFamilyIndices.graphics, 0, &queue);

        // Find a suitable depth format
        VkBool32 validDepthFormat = vks::tools::getSupportedDepthFormat(physicalDevice, &depthFormat); 
        
        // 获取创建swapChain扩展的方法指针
        swapChain.connect(instance, physicalDevice, device);
        
        // 异步处理
        // 提交处理
    }
    
    // 创建window
    HWND VulkanExampleBase::setupWindow(HINSTANCE hinstance, WNDPROC wndproc);
    
    void VulkanExample::prepare()
    {
        VulkanExampleBase::prepare();
        {
            void VulkanExampleBase::initSwapchain()
            {
                void VulkanSwapChain::initSurface(void* platformHandle, void* platformWindow)
                {
                    // 根据窗口信息创建Surface
                    vkCreateWin32SurfaceKHR(instance, &surfaceCreateInfo, nullptr, &surface);
                    // 设置colorFormat和ColorSpace
                }
            }
            
            void VulkanExampleBase::createCommandPool();
         
            void VulkanExampleBase::setupSwapChain()
            {
                swapChain.create(&width, &height, settings.vsync, settings.fullscreen);
                {
                    // 获取surface的的属性和格式
                    // 获取支持presentMode
                    // 获取surface的宽高
                    
                    // 填充VkSwapchainCreateInfoKHR结构体
                    // 创建Swapchain
                	VK_CHECK_RESULT(fpCreateSwapchainKHR(device, &swapchainCI, nullptr, &swapChain));
                    
                    // 获取vkImage数量
                    // 创建vkImageView
                }
            }
            
            // Create one command buffer for each swap chain image and reuse for rendering
            void VulkanExampleBase::createCommandBuffers();
        }
    }
}
```

## Vulkan概念

### VkInstance

初始化Vulkan library， 为驱动程序提供应用层信息

#### 创建

```cpp
VkApplicationInfo; // 提供应用信息
VkInstanceCreateInfo; // 提供扩展和校验层信息
vkCreateInstance(&createInfo, nullptr, &instance);
```

#### 销毁

```cpp
vkDestroyInstance(instance, nullptr);
```

### VkPhysicalDevice

显卡的抽象

#### 获取方式

```c++
uint32_t deviceCount = 0;
vkEnumeratePhysicalDevices(instance, &deviceCount, nullptr);
std::vector<VkPhysicalDevice> devices(deviceCount);
vkEnumeratePhysicalDevices(instance, &deviceCount, devices.data());
```

#### 设备评估

```c++
// name， type, vulkan版本
VkPhysicalDeviceProperties deviceProperties;
vkGetPhysicalDeviceProperties(device, &deviceProperties);
//支持的特性，如纹理压缩，64位浮点数和多视图渲染， geometryshader
VkPhysicalDeviceFeatures deviceFeatures;
vkGetPhysicalDeviceFeatures(device, &deviceFeatures);
```

#### 选择设备

选择支持特性最多的那个设备

### Queue Family队列族

支持不同的command的队列, VkQueueFamilyProperties结构体包含具体信息

#### 获取显卡支持的队列族

```c++
VkQueueFamilyProperties;//支持的command类型，以及支持的队列数
    
uint32_t queueFamilyCount = 0;
vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, nullptr);
std::vector<VkQueueFamilyProperties> queueFamilies(queueFamilyCount);
vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, queueFamilies.data());
```

### VkDevice

用于与物理设备交互的逻辑设备。

#### 创建

```c++
VkDeviceQueueCreateInfo; //包括队列类型，队列数量，以及队列的优先级
VkDeviceCreateInfo; // 需要指定创建的队列info以及使用的物理设备的特性，扩展和校验层
//需要physicalDevice
vkCreateDevice(physicalDevice, &createInfo, nullptr, &device)
```

#### 销毁

```cpp
vkDestroyDevice(device, nullptr);
```

### VkQueue

存储command的队列，创建VkDevice时创建

#### 检索/获取

```c++
vkGetDeviceQueue(device, indices.graphicsFamily, 0, &graphicsQueue);
```

### Vulkan Window Surface/ VkSurfaceKHR

用以呈现渲染图像使用, surface是Vulkan与窗体系统的连接桥梁

#### 创建

需要在`instance`创建之后立即创建窗体`surface`，它会影响物理设备的选择

```c++

VkWin32SurfaceCreateInfoKHR; // 包含窗口和进行的句柄

VkSurfaceKHR surface;

auto CreateWin32SurfaceKHR = (PFN_vkCreateWin32SurfaceKHR) vkGetInstanceProcAddr(instance, "vkCreateWin32SurfaceKHR");
CreateWin32SurfaceKHR(instance, &createInfo, nullptr, &surface) 
```

直接使用glfw创建

```cpp
glfwCreateWindowSurface(instance, window, nullptr, &surface)
```

#### 销毁

```cpp
vkDestroySurfaceKHR(instance, surface, nullptr);
```



### VkSwapchainKHR 

从屏幕的角度观察，交换链本质上是一个图像队列。一个图像在后台渲染，一个图形在前台显示。

#### 创建

1. 检查物理设备的扩展是否支持交换链
2. 查询交换链支持详情
   - surface的功能属性（最大最小数量，宽高）
   - 格式和颜色空间
   - presentMode
     - 三重缓冲，队列模式

```c++
// surface
// image count
// color format, Color space
// 分辨率
// 不同队列族之间的交换链图形如何处理
VkSwapchainCreateInfoKHR; 

VkSwapchainKHR swapChain;
vkCreateSwapchainKHR(device, &createInfo, nullptr, &swapChain) ;
```

### VkImage和VkImageView

使用任何的**VkImage**，包括在交换链或者渲染管线中的，我们都需要创建**VkImageView**对象。从字面上理解它就是一个针对图像的视图或容器，通过它具体的渲染管线才能够读写渲染数据，换句话说`VkImage`不能与渲染管线进行交互。

#### 创建

```c++
// vkImage的指针
// 图像的使用目标
// 
VkImageViewCreateInfo;
vkCreateImageView(device, &createInfo, nullptr, &swapChainImageViews[i]);
```

#### 销毁

```cpp
 vkDestroyImageView(device, swapChainImageViews[i], nullptr);
```
