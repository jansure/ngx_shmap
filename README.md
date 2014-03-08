ngx_shmap nginx �����ֵ�ģ�顣
===================================  
  nginx�����ֵ�ģ�顣����Ҫ�ڶ�����̼乲������ʱ������ʹ�á�(����һ�������������������ڴ滺��)
 	��ģ��ʵ�����Ǵ�ngx_lua�е�ngx_http_lua_shdictģ����ֲ�������ڴ˸�лԭ����(@agentzh)Ϊ�����ṩ�������Ŀ�Դ��Ŀ��

��Ŀ������
-----------------------------------  
	ngx_log_mod ��־���ģ�顣����nginxĬ����־���������Ҫ���������ģ�����ֱ��ʹ�����Լ�д��һ��ģ�顣
	��ҳ��ַΪ��https://github.com/jie123108/ngx_log_mod

��Ŀ����
----------------------------------- 
	cd nginx-1.x.x
	./configure --add-module=/u/GitHub/ngx_log_mod \
		--add-module=/path/to/ngx_shmap \
		--add-module=/path/to/ngx_shmap/testmod
	
	#���еڶ�����ngx_shmapģ�飬���������Լ���ģ�顣����ʾ���и������ǲ���ģ�飬������ngx_shmapĿ¼�¡�
	
	make
	make install

����˵��(ʹ��ʾ���ɲο�testmod�е�shmtest_mod.c)
-----------------------------------

��ʼ��
=====
```
����������
	ngx_shm_zone_t* ngx_shmap_init(ngx_conf_t *cf, ngx_str_t* name, size_t size, void* module)

```

��ȡֵ��
=====
```
/**
 * zone �����ֵ����
 * key Ϊ�ֵ��key.
 * data Ϊȡ�õ�����(ȡ�õ�������ֱ��ָ�����ڴ����ģ�
 *          ����������޸��˸����ݣ������ڴ��е�����Ҳ�ᱻ�޸�)
 * value_type Ϊ���ݵ�����
 * exptime Ϊ���ж�ù���(��)
 * user_flags �������õ�user_flagsֵ
 **/
int ngx_shmap_get(ngx_shm_zone_t* zone, ngx_str_t* key, 
		ngx_str_t* data, uint8_t* value_type,uint32_t* exptime,uint32_t* user_flags);
		
/**
 * ��ngx_shmap_get��ͬ��ȡ��һ��key��ֵ��
 * ��ngx_shmap_get��֮ͬ������user_flags���ص������õ�
 *    user_flags��ָ�룬�����ڻ�ȡ���user_flags�����޸ġ�
 **/
int ngx_shmap_get_ex(ngx_shm_zone_t* zone, ngx_str_t* key, 
		ngx_str_t* data, uint8_t* value_type,uint32_t* exptime,uint32_t** user_flags);

//��ȡint32���͵�ֵ��������ĺ����ļ򵥵İ�װ��	
int ngx_shmap_get_int32(ngx_shm_zone_t* zone, ngx_str_t* key, int32_t* i);
//��ȡint64���͵�ֵ��������ĺ����ļ򵥵İ�װ��	
int ngx_shmap_get_int64(ngx_shm_zone_t* zone, ngx_str_t* key, int64_t* i);
//��ȡint64���͵�ֵ������ֵ������0.
int ngx_shmap_get_int64_and_clear(ngx_shm_zone_t* zone, ngx_str_t* key, int64_t* i);

```

ɾ��key
=====
```
int ngx_shmap_delete(ngx_shm_zone_t* zone, ngx_str_t* key);
```

ѭ���������������ֵ�
====
```
typedef void (*foreach_pt)(ngx_shmap_node_t* node, void* extarg);
#funcΪ����ص�����
int ngx_shmap_foreach(ngx_shm_zone_t* zone, foreach_pt func, void* args);

```

����ɾ������
=====
```
//��������ֵ�
int ngx_shmap_flush_all(ngx_shm_zone_t* zone);
//��չ��ڵ�key
int ngx_shmap_flush_expired(ngx_shm_zone_t* zone, int attempts);
```

��ӣ��滻������ֵ
=====
```
//���һ��key,value, ������ڻᱨ��(�ռ䲻��ʱ����ɾ��������ڵ�����)
int ngx_shmap_add(ngx_shm_zone_t* zone, ngx_str_t* key, ngx_str_t* value, 
									uint8_t value_type, uint32_t exptime, uint32_t user_flags);
//���һ��key,value, ������ڻᱨ��(�ռ䲻��ʱ���᷵��ʧ��)
int ngx_shmap_safe_add(ngx_shm_zone_t* zone, ngx_str_t* key, ngx_str_t* value, 
									uint8_t value_type, uint32_t exptime, uint32_t user_flags);
//�滻һ��key,value
int ngx_shmap_replace(ngx_shm_zone_t* zone, ngx_str_t* key, ngx_str_t* value, 
									uint8_t value_type, uint32_t exptime, uint32_t user_flags);
//����һ��key,value.
int ngx_shmap_set(ngx_shm_zone_t* zone, ngx_str_t* key, ngx_str_t* value, 
									uint8_t value_type, uint32_t exptime, uint32_t user_flags);

//��key����i,���������Ӻ��ֵ��
int ngx_shmap_inc_int(ngx_shm_zone_t* zone, ngx_str_t* key,int64_t i,
															uint32_t exptime, int64_t* ret);
//��key����d,���������Ӻ��ֵ��
int ngx_shmap_inc_double(ngx_shm_zone_t* zone, ngx_str_t* key,double d,
															uint32_t exptime,double* ret);
```

