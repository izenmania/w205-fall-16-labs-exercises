MIDS W205

Lab#              8         LabTitle          IntroductiontoUsingTableauWithHive

RelatedModule(s)  9         Goal              IntroductiontoUsingTableauWithHive
LastUpdated       10/14/15  ExpectedDuration  30­40minutes

BITableauVisualizationUsingHiveWarehouse

Introduction

Tableauisoneoftheworld'sfastest-growingbusinessintelligencecompanies.Tableauoffersto
quicklyanalyze,visualize,andshareinformation.AswithmostBItools,TableaucanuseApache
Hive(viaODBCconnection)asthedefactostandardforSQLaccessinHadoop.

Wewillcoverthefollowingtopicsinthislab:

        CreatingaHivetableandrunningasamplequeryonHive
        InstallingTableauandODBCdriverforconnectingtoHive
        ConfiguringandconnectingtoHadoopHivefromTableauusingODBCdriver
        BuildvisualizationonWeblogClickstreamAnalyticsusingTableau


Step1:CreatingHiveTableandRunningaSampleQueryonHive

Thefollowingcodecreatesatable--Web_Session_Log--fromtheweblogdataintroducedin
previouslabs.WhilethislabusesTableautoexploretheweblogdata,feelfreetoproceedwith
anytableyouwant.


         CREATE TABLE Web_Session_Log(
                  DATETIME varchar(500), USERID varchar(500),
                  SESSIONID varchar(500),PRODUCTID varchar(500),
                  REFERERURL varchar(500)

         )
         ROW FORMAT DELIMITED FIELDS TERMINATED BY "\t"
         STORED AS textfile
         tblproperties("skip.header.line.count"="1");




                                                                       1
LoadDataintotheTable:



FromHDFS:


         LOAD DATA INFILE `/mnt/weblog/weblog.csv'
         INTO TABLE Web_Session_Log;

FromLocalFileSystem:

         LOAD DATA LOCAL INFILE `/mnt/weblog/weblog.csv'
         INTO TABLE Web_Session_Log;



SampleHiveQuery


ThisisaquerytofindoutwhichREFERERURLhasbeenreferredandforhowmanytimes.


         SELECT REFERERURL, count(*)
         FROM Web_Session_Log
         GROUP BY REFERERURL;



Pleaseseebelowfortheexactcode:


                                                                                                                                            



                                                                       2
Step2:StartingaHiveThriftServerforRemoteHiveAccess

HiveServer2isaserverinterfacethatallowsremoteclientstoexecutequeriesagainstHive.In
Tableau,wewillbeabletoextractdatafromourWeb_Session_Logtablebysendingrequests
throughHiveServer2.



Howtostart:

         hive ­service hiveserver2
or

           

         hive ­service hiveserver ­help
         hive ­service hiveserver

           

(NotethatasofHive1.0.0,HiveServerwasremovedinfavorofHiveServer2.Itisrecommended
youuseHiveServer2.)


Hereisaconnectionstringforyourreference:

           

withusernameandpassword:
         jdbc:hive2://myhost.example.com:21050/test_db;user=fred;pas
         sword=xyz123



withoutusernameandpassword:
         jdbc:hive2://ec2-54-157-182-212.compute-
         1.amazonaws.com:10000/default

Step3:InstallingTableauandODBCDriverforConnectingtoHive

HiveisadatawarehousetechnologyforworkingwithdatainyourHadoopclusterusinga
combinationoftraditionalSQLexpressionsandadvancedHadoop-specificdataanalysisand
transformationoperations.TableauworkswithHadoopusingHivetoprovideauserexperience
thatrequiresnoprogramming.

Inthislab,wewillconnectTableauDesktopProwiththeHiveServerinordertoaccesstheHive
tableWeb_Session_Log,whichwecreatedearlier.


ToinstallTableauonyourdesktop(TableauDesktopProEdition):

YoucandownloadTableauDesktopProdirectlyfromTableau'swebsite.SelecttheWindowsor
Macversionbasedonyouroperatingsystem(32-bitor64-bit).

Oncetheproductisdownloaded,youcaninstallitbydouble-clickingtheinstallerpackage.

                                                                       3

                                                                                                                                     



ToinstalltheODBCdriverforHive:

ForbothHiveServerandHiveServer2,youmustinstalltheCloudera,Hortonworks,MapR,or
AmazonEMRODBCdriverfromtheDriverspage.Ensurethattheversionofthedriveryou
downloadmatchesthebitversionofyouroperatingsystem.

     Cloudera(Hive):ClouderaODBCDriverforApacheHive2.5.x,32-bitor64-bit
             o ForusewithTableauServer8.0.0­8.0.7;for8.1.0­8.1.3,useversion2.5.0.1000.
             o ForusewithTableauServer8.0.8andlater;for8.1.4andlater,usedriverversion
                  2.5.0.1001orlater.
             o Clouderadriverscanbefoundhere.

     Hortonworks:HortonworksHiveODBCDriver1.2.x(32-bitor64-bit)
     MapR:MapR_odbc_2.1.0_x86.exeorlater,orMapR_odbc_2.1.0_x64.exeorlater
     AmazonEMR:HiveODBC.ziporImpalaODBC.zip
Note:Ifyouhaveadifferentversionofthedriverinstalled,uninstallthatdriverbeforeinstalling
theversionprovidedontheClouderawebsite.


Step4:ConfiguringandConnectingtoHadoopHiveFromTableau
UsingODBCDriver(WindowsOnly)

AfterinstallingtheappropriateODBCdriver,youneedtoconfiguretheODBCSystemDSNon
Windows.DownloadthedriverfortheappropriateversionofTableauDesktop(x86orx64),
andinstallit.GotoControlPanel->System&Security->AdministrativeTools->ODBCData
sources(32-bitor64-bit).

OpentheODBCdatasource,gototheSystemDSNtab,andconfigureitbyapplyingHost
(Hadoopservername),Port(10000),Database(default),HiveServerType(HiveServer2),andthe
authenticationpanel,thenentertheusernameandpasswordcredentials(ifyouconfigured
securitysettingsonserverauthentication).

                                                                       4
                                                                                                           
                                                                        

ClickTesttochecktheconnectivityofTableautotheHadoopserver.Iftheconnectionis
successful,asuccessmessageisdisplayed.Otherwise,anerrormessageisdisplayed.


                                                                                                   


                                                                       5
Step5:ConnectTableautoHiveServer/HiveServer2UsingODBC
Driver

OpenTableauDesktopProfromitsquick-launchoption.

                                                                                                

Next,gotoData->NewDataSourcetab,andclickClouderaHadooptoconnecttotheHadoop
server.

                                                                                          

TheClouderaHadoopconnectionpaneopens.EntertheHadoopservercredentialstoconnect.
ToconnecttotheHiveServer,thedefaultportnumbershouldbe10000,theTypeshouldbe
HiveServer2,andtheusernameneedstobeprovided.

                                                                                                           
                                                                       6



Step6:BuildVisualizationsonWeblog,ClickstreamAnalyticsUsing
Tableau

Afterthedatasourceconnectionismadesuccessfully,youcanconnecttotheHivetable.Click
ExtracttogetdatalocallyandstoreTableauinmemoryreducelatencybecauseHiveisbased
onabatch-processingmechanism.SwitchbacktoTableau.UndertheDataConnectionpane,
entertheschemaname,enterdefault,andclicktheSearchiconontherightsideofthe
textbox.Next,enterthetablenameWeb_Session_LogintheTablenametextbox,clickthe
Searchiconontherightsideofthetextbox,andselectanddragthetabletotheupper-right
sideoftheTableauwindow,asshowninthefollowingscreenshot.

                                                                                                                                    

Nowyoucanextractthedatalocallyonanin-memoryTableaudatasettoavoidunnecessary
serverlatency.

                                                                                                              

ClickAutomaticupdatetorefreshthedataset.Itshouldlooklikethefollowingscreen:

                                                                                                                                     
                                                                        
                                                                       7
ClicktheWorksheettab,andselectNewWorksheettostartbuildingvisualizations.
Onthenewworksheettab,ontheDatapane,dragtheweb_session_log.sessionidfield
totheMeasurespane.

                                                                                                  

Next,dragthefieldtotheRowsshelf.Hoveroverthefieldtoexpandthedrop-downmenu.
Changetheaggregationmethod(underMeasure)fromSumtoCount(Distinct).

                                                                                                           

Dragtheweb_session_log.refererurl fieldtotheColumnshelf,thenright-clickthe
valuetocreateafiltertovisualizethetopfivereferringURLs.SelectFilter,andclickthe
ConditiontabtogetURLsbasedonweb_session_log.sessionidsbeyondacertainvalue
(e.g.,web_session_log.sessionid >= 3,996)todrilldowntothetopfivereferringURLs.

                                                                                                     
                                                                        

Afteryoudefinethesettings,changethecharttypeintheShowMepaneontherightsideof

                                                                       8
thewindow.

                                                                                     
                                                                        

Selectthebubblescharttype.Youwillseethegraph,"Top5ReferringURLs,"asfollows:

                                                                                                             


Next,createanewworksheetandnameitTopReferringURLsoverlast10years.Drag
web_session_log.sessionidtotheRowsshelf.
OntheDatapane,right-clickonthedatetimefield,andchangeitsdatatypetoDateand

                                                                       9
Time.Then,dragittotheColumnsshelf.DragtheRefererURLfieldtotheColorsectionofthe
Markspane.IntheShowMesection,selectthelinescharttype.

                                                                                                                         


Gotoanewworksheettab,andnameitTop10userswhousedtop10products.Onthis
worksheettab,dragthesessionidfieldtotheRowsshelfandtheuseridfieldtothe
Columnsshelf.Filtertheusersbasedonthemaximumsessioncount.Youneedtofilterthe
useriddatabasedonconditionofthesessionidvalue.DragtheproductIDfieldtothe
colorfieldintheMarkspanetodifferentiatebetweenproducts.Youmayselectthesimple
stacked-barcharttype.

                                                                      10
                                                                                                                
                                                                        

Once,youbuildsuchfewgraphs,clicktheDashboardtabontopoftheTableauwindow

                                                                                                      
                                                                        

SelectNewDashboardtostartimplementingtheweblog-clickstreamanalyticsdashboard.
Onthenewdashboardpage,dragtheexistingimplementedworksheetgraphstotheright
pane.

                                                                      11
                                                                                                                                          
                                                                        

Whenyoudraganddroptheimplementedgraphsontheexitingworksheet,youwillseethe
WeblogClickstreamanalyticsdashboardonTableauDesktop,asshowninthefollowing
screenshot:

                                                                                                                                          


YoucanalsopublishtheworkbookonTableauServerifyouhavethosecredentials.Alwayssave
theworkbookonyourlocaldriveusingtheFile->ExportasPackagedworkbookoption.Youcan

                                                                      12
alsoprinttheentireworkbookorselectedworksheetsinPDFformatasrequired.

                                                                                                      
                                                                        

TheTableauweblog-hive-clickstreamanalyticsworkbookisprintedinPDFformat.


                                                                                                        



Task:SubmittheprintedPDFweblog-hive-clickstreamanalyticsworkbook


13
