#组织组件概述 

## 业务需求 ##

企业系统开发中，经常需要维护企业的组织结构，业务会根据企业的组织进行管理，因此我们提供基本的组织组件，业务可以根据自己的需要进行扩张使用。

# 整体设计 #

## 依赖环境 ##

组件采用Maven进行编译和打包发布，其对外提供的依赖方式如下：

```
	<dependency>
      <groupId>com.yonyou.iuap</groupId>
      <artifactId>iuap-organization</artifactId>
      <version>${iuap.modules.version}</version>
    </dependency>
```

${iuap.modules.version} 为平台在maven私服上发布的组件的version。

## 功能说明 ##

组织组件主要是提供组织的核心模型和基本服务，支持通过组织职能的方式对组织进行扩展。

1.	提供组织的核心模型和基本服务
2.	提供组织基本信息的管理；
3.	提供组织职能的管理；
4.	支持通过组织职能的方式对组织进行扩展；
5.	支持对组织基本信息和组织职能的扩展；

# 使用说明 #

## 使用方法

1. 	配置spring文件，参考示例工程：organization-applicationContext.xml

2. 执行数据库脚本

	依次执行examples项目下sql目录中的dll.sql、index.sql、dml.sql建立数据库并初始化数据

3. 字段结构如下：

#####（1）组织表org_orgs

<table>
   <tr>
      <td>名称</td>
      <td>代码</td>
      <td>注释</td>
   </tr>
   <tr>
      <td>组织Id</td>
      <td>id</td>
      <td></td>
   </tr>
   <tr>
      <td>组织编码</td>
      <td>code</td>
      <td></td>
   </tr>
   <tr>
      <td>组织名称</td>
      <td>name</td>
      <td></td>
   </tr>
   <tr>
      <td>上级组织Id</td>
      <td>fatherid</td>
      <td></td>
   </tr>
   <tr>
      <td>组织描述</td>
      <td>description</td>
      <td>对组织的简要描述</td>
   </tr>
   <tr>
      <td>租户Id</td>
      <td>tenantid</td>
      <td></td>
   </tr>
   <tr>
      <td>应用系统Id</td>
      <td>sysid</td>
      <td></td>
   </tr>
   <tr>
      <td>内部编码</td>
      <td>innercode</td>
      <td>构建编码树，用于查询某个组织的所有下级</td>
   </tr>
   <tr>
      <td>组织类型一</td>
      <td>orgtype1</td>
      <td>组织类型，目前是十个，支持扩展</td>
   </tr>
   <tr>
      <td>组织类型二</td>
      <td>orgtype2</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型三</td>
      <td>orgtype3</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型四</td>
      <td>orgtype4</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型五</td>
      <td>orgtype5</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型六</td>
      <td>orgtype6</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型七</td>
      <td>orgtype7</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型八</td>
      <td>orgtype8</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型九</td>
      <td>orgtype9</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型十</td>
      <td>orgtype10</td>
      <td></td>
   </tr>
</table>
 
#####（2）组织类型表org_type

<table>
   <tr>
      <td>名称</td>
      <td>代码</td>
      <td>注释</td>
   </tr>
   <tr>
      <td>组织类型id</td>
      <td>id</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型编码</td>
      <td>code</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型名称</td>
      <td>name</td>
      <td></td>
   </tr>
   <tr>
      <td>租户id</td>
      <td>tenantid</td>
      <td></td>
   </tr>
   <tr>
      <td>应用系统id</td>
      <td>sysid</td>
      <td></td>
   </tr>
   <tr>
      <td>组织类型的字段名</td>
      <td>fieldname</td>
      <td>字段名代码的是组织的类型，如orgtype1</td>
   </tr>
   <tr>
      <td>组织类型的服务类</td>
      <td>serviceclass</td>
      <td>每个组织类型扩展一张表，这个字段用于注册服务类</td>
   </tr>
   <tr>
      <td>组织类型的实体类</td>
      <td>entityclassname</td>
      <td>每个组织类型的实体类，若是组织类型为公司，就是公司的实体类</td>
   </tr>
</table>


3，服务接口类

（1）IOrgService，主要提供组织相关的接口：

```
    public interface IOrgService {
	
		public void save(OrgEntity orgEntity);

		public void deleteById(String id);
	
		public OrgEntity queryById(String id);
	
		/**
	 	* 具有某种组织类型的组织
	 	* @param orgtype  传入字符串类似为orgtype1
		 * @return
		 */
		public List<OrgEntity> queryOrgsByOrgType(String orgtype);
	
		/**
		 * 查询具有某几种组织类型的组织
		 * @param orgtypeArray  传入参数为例如new String[]{"orgtype1","orgtype2",...}
	 	* @return
	 	*/
		public List<OrgEntity> queryOrgsByOrgTypeArray(String[] orgtypeArray);

		public OrgEntity queryParentOrg(String id);

		public List<OrgEntity> queryChildrenOrgs(String id);
	
		public List<OrgEntity> queryAllOrgs();
	
		public List<OrgEntity> queryOrgsByIds(String[] ids);
	
		public List<OrgEntity> queryOrgsByIdList(List<String> idList);
    }
```

（2）IOrgTypeService，主要提供组织职能的相关接口：

```
    public interface IOrgTypeService {

	    public void save(OrgTypeEntity orgTypeEntity);
		
	    public void deleteById(String id);
	
	    public OrgTypeEntity queryOrgTypebyId(String id);
	
	    public OrgTypeEntity queryOrgTypeByCode(String code);
	
	    public OrgTypeEntity queryOrgTypeByName(String name);
	
		public OrgTypeEntity queryOrgTypeByFieldName(String fieldname);
	
		public OrgTypeEntity queryOrgTypeByServiceClassName(String serviceclass);
	
		public OrgTypeEntity queryOrgTypeByEntityClassName(String entityclassname);
	
		public List<OrgTypeEntity> queryOrgTypesByIds(String[] ids);

		public OrgTypeEntity[] queryOrgTypeByOrg(OrgEntity orgEntity); 

		public OrgTypeEntity[] queryOrgTypeByOrgID(String orgId);
		
		/**
		 *判断该OrgEntity中的对应orgtype是否被选中
	 	* @param orgEntity
	 	* @param orgtypeId
		 * @return
	 	*/
		public boolean isTypeOf(OrgEntity orgEntity, String orgtypeId);
	
		/**
		 * 判断该orgId对应的OrgEntity中的对应orgtype是否被选中
		 * @param orgId
		 * @param orgtypeId
		 * @return
		 */
		public boolean isTypeOfByOrgId(String orgId, String orgtypeId);
	
    }
```

## 扩展开发说明

#### 1，扩展组织基本信息

例如，我们想要增加描述组织信息的字段，可以扩展组织表org_orgs，修改服务IOrgService的默认实现，在spring中配置即可。

也可在具体的组织类型上增加相应组织类型特有的字段，对应的服务类和实体类自己实现，参照组织职能扩展，对应的pk_org存为该数据同步到组织表中的主键。

#### 2，组织职能扩展

例如，我们想要增加公司这一主职职能，应在org_type中注册一条组织职能的相关信息,注册组织类型对应的服务类与实体类，并进行相应的实现。
<table>
    <tr>
        <td>组织类型id（id）</td>
        <td>组织类型编码（code)</td>
        <td>组织类型名称(name)</td>
		<td>租户id(tenantid)</td>
		<td>应用系统id(sysid)</td>
		<td>组织类型的字段名(fieldname)</td>
		<td>组织类型的服务类(serviceclass)</td>
		<td>组织类型的实体类(entityclassname)</td>
    </tr>
    <tr>
        <td>00229d33-2807-4ee9-9aca-ef9e4311ef46</td>
        <td>corp</td>
        <td>公司(name)</td>
		<td></td>
		<td></td>
		<td>orgtype1</td>
		<td>com.yonyou.iuap.org.service.ICorpService</td>
		<td>com.yonyou.iuap.org.entity.CorpEntity</td>
    </tr>
</table>

最后，要将扩展的组织数据同步到org_orgs中，pk相同，组织表中的orgtype1字段存为'Y'

