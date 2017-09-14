# AppX-Rome
Rome wasn't built in a day. 罗马不是一天建成的。
## 2017-09-14 ( iOS 比牛已经确定的第三方库版本固定说明)

## 比牛当前cocopods倒入的第三方库有：

AFNetworking          3.1.0

MJRefresh              3.1.12

MJExtension            3.0.13

SDWebImage           4.0.0

SVPullToRefresh      0.4.1

MBProgressHUD      1.0.0

YTKNetwork             2.0.3

GRMustache             7.3.2

CloudMusic              0.0.80

FMDB                       2.6.2


还有一些没有用cocopods 倒入的第三方库没法确定版本：
GQImageViewer


## 详细说明
SDWebImage 和AFNetworking在项目中用到的地方最多，而SDWebImage版本升级后，方法几乎没有什么变动。
AFNetworking从2.xx 升级到3.xx变动最大，影响最广，故单独对AFNetworking升级的版本做说明。

1、AFNetworking在3.XX版本中删除了基于 NSURLConnection API的所有支持(在Xcode 7中，NSURLConnection的API已经正式被苹果弃用。)现已完全基于NSURLSession的API，这降低了维护的负担，而3.1.0版本比较稳定，目前可以长期使用此版本，更换所以选择升级到3.1.0 版本
在文件目录结构上3.XX版本去除了NSURLConnection这个文件夹和里面的
AFHTTPRequestOperation.h
AFHTTPRequestOperation.m
AFHTTPRequestOperationManager.h
AFHTTPRequestOperationManager.m
AFURLConnectionOperation.h
AFURLConnectionOperation.m

2、从2.xx版本升级到3.xx的版本对项目的影响是项目中所有的请求方法都得更换
核心方法：
> AFHTTPRequestOperationManager *mgr = [AFHTTPRequestOperationManager manager];
>     mgr.requestSerializer = [AFHTTPRequestSerializer serializer];
>     mgr.responseSerializer = [AFJSONResponseSerializer serializer];
>     
>     	
>     	
>     	 NSDictionary *params = @{ @"PPID":[NSString stringWithFormat:@"%ld", (long)ppId],
>                               @"UserID":userId,
>                             };
>     
>    
>     [mgr POST:url parameters:params success:^(AFHTTPRequestOperation *operation, id responseObject) {
>         NSDictionary* jsonRsp = (NSDictionary *)responseObject;
>         if (isSuccessRsp(jsonRsp))
>         {
>             success(jsonRsp);
>         }
>         else
>         {
>             failure(errorMsgRsp(jsonRsp));
>         }
>     } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
>         failure(error.description);
>     }];


> - (AFHTTPRequestOperation *)POST:(NSString *)URLString
>                   headParameters:(id)headParameters
>                   bodyParameters:(id)bodyParameters
>                          success:(void (^)(AFHTTPRequestOperation *operation, id responseObject))success
>                          failure:(void (^)(AFHTTPRequestOperation *operation, NSError *error))failure
> {
>    
>     [self setHeadParameters:headParameters];
>     
>    
>     NSMutableURLRequest *request = [self.requestSerializer requestWithMethod:@"POST" URLString:[[NSURL URLWithString:URLString] absoluteString] parameters:bodyParameters error:nil];
>     
>     
>    
>     AFHTTPRequestOperation *operation = [self HTTPRequestOperationWithRequest:request success:success failure:failure];
>     
>     
>     
>     
>     
>     [self.operationQueue addOperation:operation];
>     
>     
>     return operation;
> }

更换成以下方法：

> 
> - (void)POST:(NSString *)URLString
>            headParameters:(id)headParameters
>            bodyParameters:(id)bodyParameters
>                   success:(void (^)( id responseObject))success
>                   failure:(void (^)( NSError *error))failure
> {
> 
> 
> 
>     NSURL *url= [NSURL URLWithString:URLString];
>     if (url == NULL)
>     {
>         return;
>     }
>     
>     AFHTTPSessionManager *sessionManager = [AFHTTPSessionManager manager];
>     
>     if (headParameters)
>     {
>         [self setHeadParameters:headParameters sessionMgr:sessionManager];
>     }
> 
>     [sessionManager POST:
>     
>     [url absoluteString] parameters:bodyParameters progress:^(NSProgress * _Nonnull uploadProgress) {
>     
>     
>     } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
>       
>       
>         success(responseObject);
>         
>     } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
>     
>     
>     
>         failure(error);
>     }];
> }

