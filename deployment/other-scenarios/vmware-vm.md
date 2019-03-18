# VMware虚机环境

{% hint style="danger" %}
道云只支持物理机作为PX的生产环境，**不支持虚拟机作为PX的生产环境。**
{% endhint %}

{% hint style="danger" %}
本章节描述在VMware虚拟机环境中**测试PX**时，如何尽可能的让PX稳定运行。**如果虚拟机环境不满足此章的要求，无论PX出现的任何问题，道云都不予解答**。
{% endhint %}

## VMware虚机的配置要求

{% hint style="info" %}
指导原则是要求**最高级别的资源优先级**，禁止其它任何虚机同PX的虚机抢夺资源。

有\*的要求是尽力而为
{% endhint %}

<table>
  <thead>
    <tr>
      <th style="text-align:left">#</th>
      <th style="text-align:left">&#x8981;&#x6C42;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">1</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x73AF;&#x5883;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">PX&#x7684;&#x5B58;&#x50A8;&#x8282;&#x70B9;&#x5FC5;&#x987B;&#x8DD1;&#x5728;&#x72EC;&#x7ACB;&#x7684;&#x865A;&#x673A;&#x91CC;&#x3002;&#x8FD9;&#x4E2A;&#x865A;&#x673A;&#x4E0D;&#x8DD1;&#x4EFB;&#x4F55;&#x5176;&#x5B83;&#x7684;&#x4E1A;&#x52A1;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">vCPU&#x8D44;&#x6E90;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x7684;vCPU&#x8D44;&#x6E90;&#x5FC5;&#x987B;&#x5168;&#x90E8;&#x9884;&#x7559;(Reserve)</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">vCPU&#x5355;&#x69FD;&#x4F4D;</td>
      <td style="text-align:left">vCPU&#x914D;&#x7F6E;&#x5355;&#x69FD;&#x4F4D;(Socket)&#xFF0C;&#x56E0;&#x4E3A;PX&#x8FDB;&#x7A0B;&#x53EA;&#x80FD;&#x8BC6;&#x522B;&#x4E00;&#x4E2A;&#x69FD;&#x4F4D;&#x7684;vCPU</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">&#x5185;&#x5B58;&#x8D44;&#x6E90;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x7684;&#x5185;&#x5B58;&#x8D44;&#x6E90;&#x5FC5;&#x987B;&#x5168;&#x90E8;&#x9884;&#x7559;&#x9501;&#x5B9A;(Reserve
        and Lock)</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">&#x6839;&#x76D8;&#x8D44;&#x6E90;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">
        <p>&#x865A;&#x673A;&#x7684;&#x6839;&#x76D8;&#x5FC5;&#x987B;&#x662F;&#x6765;&#x81EA;<b>&#x975E;&#x5171;&#x4EAB;</b>&#x7684;Datastore,</p>
        <p>&#x5E76;&#x4E14;&#x662F;&#x539A;&#x7F6E;&#x5907;&#x7F6E;&#x96F6;(Thick
          Provisioning Eager Zero)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">&#x6570;&#x636E;&#x76D8;&#x8D44;&#x6E90;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">
        <p>&#x865A;&#x673A;&#x7684;&#x6570;&#x636E;&#x76D8;&#x5FC5;&#x987B;</p>
        <p>&#x662F;RAID&#x5361;&#x7684;PCI&#x7A7F;&#x900F;(PCI Pass Through)
          <br />&#x6216;&#x8005;&#x662F;&#x88F8;&#x8BBE;&#x5907;&#x6620;&#x5C04;(Raw Device
          Mapping)</p>
        <p><b>&#x6216;&#x8005;&#x865A;&#x62DF;&#x76D8;VMDK&#x7528;dd&#x5199;&#x96F6;</b>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">7*</td>
      <td style="text-align:left">&#x4F18;&#x5148;&#x54CD;&#x5E94;</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x7684;&#x5EF6;&#x8FDF;&#x654F;&#x611F;&#x5EA6;(Latency
        Sensitivity)&#x5FC5;&#x987B;&#x8BBE;&#x7F6E;&#x4E3A;&#x9AD8;(High)</td>
    </tr>
    <tr>
      <td style="text-align:left">8*</td>
      <td style="text-align:left">NUMA&#x7ED1;&#x5B9A;</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x7684;&#x5FC5;&#x987B;&#x9501;&#x5B9A;&#x5728;&#x4E00;&#x4E2A;NUMA
        NODE&#x4E0A;</td>
    </tr>
    <tr>
      <td style="text-align:left">9*</td>
      <td style="text-align:left">&#x7F51;&#x5361;&#x72EC;&#x4EAB;</td>
      <td style="text-align:left">&#x865A;&#x673A;&#x91CC;&#x7684;PX&#x4F7F;&#x7528;PCI&#x7A7F;&#x900F;&#x7684;&#x7F51;&#x5361;</td>
    </tr>
  </tbody>
</table>## 虚拟盘\(VMDK\)如何用dd写零

{% hint style="danger" %}
对裸设备做dd写零会彻底抹除数据，请务必确认是空盘，而且在PX安装之前操作。如果dd失败报错，或者dd速度小于50MB/s, **则此虚拟盘不可用。**
{% endhint %}

```text
dd if=/dev/zero of=/dev/sdX bs=1M oflag=sync status=progress
```

如果盘上已经有数据，例如根盘，请按以下操作。

```text
dd if=/dev/zero of=/dd.file bs=1M oflag=sync status=progress
# 切勿重启
rm -f /dd.file
```

## 网卡类型

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x7F51;&#x5361;&#x7C7B;&#x578B;</th>
      <th style="text-align:left">&#x989D;&#x5916;&#x8981;&#x6C42;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x7F51;&#x5361;PCI&#x7A7F;&#x900F;</td>
      <td style="text-align:left">&#x6700;&#x4F73;&#xFF0C;&#x5EF6;&#x8FDF;&#x6700;&#x4F4E;</td>
    </tr>
    <tr>
      <td style="text-align:left">VMXNET3&#x865A;&#x62DF;&#x7F51;&#x5361;</td>
      <td style="text-align:left">
        <p>&#x865A;&#x673A;&#x5185;&#x5FC5;&#x987B;&#x5B89;&#x88C5;vmtools&#xFF1B;</p>
        <p>&#x5FC5;&#x987B;&#x5168;&#x9884;&#x7559;&#x865A;&#x673A;&#x7684;CPU&#x548C;&#x5185;&#x5B58;&#x8D44;&#x6E90;&#xFF0C;&#x7F51;&#x7EDC;&#x5EF6;&#x8FDF;&#x624D;&#x80FD;&#x7A33;&#x5B9A;</p>
      </td>
    </tr>
  </tbody>
</table>## 配置截图示例

### vCPU配置

![](../../.gitbook/assets/image%20%289%29.png)

### 内存配置

![](../../.gitbook/assets/image%20%2810%29.png)

### 根盘配置

![](../../.gitbook/assets/image%20%284%29.png)

### 数据盘RAID卡PCI穿透

![](../../.gitbook/assets/image%20%287%29.png)

### 延迟敏感度配置

![](../../.gitbook/assets/image%20%288%29.png)

### NUMA NODE绑定

![](../../.gitbook/assets/image.png)

### 网卡PCI穿透

![](../../.gitbook/assets/image%20%283%29.png)



