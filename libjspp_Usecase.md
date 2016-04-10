### libjspp Use cases ###

# Introduction #

This Library has two ways to export
  * **Anonymous Object:** Creating named Non C++ objects inside Javascript engine from C++ dynamically & defining properties and attaching native C++ methods as functions of that object.

  * **Export Native Class:**  Exporting existing C++ class as javascript class which includes exposing
  1. xposing native constructor [only One](currently.md)
  1. xposing native methods
  1. xposing native properties
  1. ser can create as many instances of these ProxyObjects

### Example:Anonymous Object: ###
```
///Native functions to be exported as method of Annonymous javascript 
#include "anonymoustest.h"
#include "anonymousjsobjectproxy.h"
#include "anonymousjsobject.h"
#include "sproxyFunc_inc.h"

#include "js_console.h"
#include "jsfunctionCallHandle.h"
#include "propertydef.h"
#define FAIL false;
void atestfunc()
{
	std::cout << "testfunc called\n";
}

void atestfunca(std::string a)
{
	std::cout << "atestfunca called a= " << a << "\n";
}
void atestfuncb(std::string& a)
{
	std::cout << "atestfuncb calleda= " << a << "\n";
}
void atestfuncc(int& a)
{
	std::cout << "atestfuncc a= " << a << "\n";
}
void atestfuncd(double& a)
{
	std::cout << "atestfuncd a= " << a << "\n";
}

void atestfunc10args(int& a1, int& a2, int& a3, int& a4, int& a5, int& a6, int& a7, int& a8, int& a9, int& a10)
{
	std::cout << "testfunc a1= " << a1 << "\n";
	std::cout << "testfunc a2= " << a2 << "\n";
	std::cout << "testfunc a3= " << a3 << "\n";
	std::cout << "testfunc a4= " << a4 << "\n";
	std::cout << "testfunc a5= " << a5 << "\n";
	std::cout << "testfunc a6= " << a6 << "\n";
	std::cout << "testfunc a7= " << a7 << "\n";
	std::cout << "testfunc a8= " << a8 << "\n";
	std::cout << "testfunc a9= " << a9 << "\n";
	std::cout << "testfunc a10= " << a10 << "\n";
}


struct TestFunctionsInClass{
   void atestfunc10args(int& a1, int& a2, int& a3, int& a4, int& a5, int& a6, int& a7, int& a8, int& a9, int& a10)
   {
       std::cout <<a1 << "," << a2<< ","  << a3<< ","  <<  a4<< "," <<  a5<< "," <<
        a6<< "," << a7<< "," <<  a8<< "," <<  a9<< ","  <<  a10 << "\n";
   }
   static void satestfunc10args(int& a1, int& a2, int& a3, int& a4, int& a5, int& a6, int& a7, int& a8, int& a9, int& a10, const void * pthis)
   {
       TestFunctionsInClass * pTestFunctionsInClass = (TestFunctionsInClass*)pthis;
       pTestFunctionsInClass->atestfunc10args(a1, a2, a3, a4, a5, a6, a7, a8, a9, a10);
   }  
};

anonymoustest::anonymoustest()
{
}

anonymoustest::~anonymoustest()
{
}


bool anonymoustest::test(void)
{
	JSEngine _js;
	int iret =  _js.Init();
	if(iret!=0)
	{
		printf("init failed\n");
	}
    try {
	defConsole(_js);
    }
    catch (...)
    {
        return FAIL;
    }
	AnonymousJSObject *pdj = new AnonymousJSObject("RECORD",_js);
	std::string sval = "1234 6";
	pdj->SetPropertyVal((char*)"CALLER_NUMBER","1234",false,false);
	pdj->SetPropertyVal((char*)"CALLER_NUMBER","1234",false,false);
	pdj->SetPropertyVal((char*)"CALLED_NUMBER","4321",false,false);
	pdj->DefPropertyValExternalString("shx", sval);
	pdj->SetPropertyValExternalString("shx", "1234 1234 1234 1234 1234 1234 1234 1234 1234 12345 13");
	pdj->addFunc<atestfunc>("testfunc", atestfunc);
	
	pdj->addFunc<void, std::string, atestfunca>("testfunca", atestfunca);
	pdj->addFunc<void, std::string&, atestfuncb>("testfuncb", atestfuncb);
	pdj->addFunc<void, int&, atestfuncc>("testfuncc", atestfuncc);
	pdj->addFunc<void, double&, atestfuncd>("testfuncd", atestfuncd);

	pdj->addFunc<void, int&, int&, int&, int&, int&, int&, int&, int&, int&, int&, atestfunc10args>("testfunc10args", atestfunc10args);

    TestFunctionsInClass tempObj;
	pdj->addFuncWithPvtData<void, int&, int&, int&, int&, int&, int&, int&, int&, int&, int&,
    (&TestFunctionsInClass::satestfunc10args) >("testfunc10argspvt", &TestFunctionsInClass::satestfunc10args, (void*)&tempObj);

	int eret = _js.CompileExecuteFile("/home/libcdr/test.js");

}


bool anonymoustest::testCallBack(void)
{
	JSEngine _js;
	int iret =  _js.Init();
	if(iret!=0)
	{
		printf("init failed\n");
	}
	defConsole(_js);
	AnonymousJSObject *pdj = new AnonymousJSObject("CallBackObj",_js);
	std::string sval = "1234 6";
    jsfunctionCall fc;
    fc.Init(_js, pdj->getJSObject(), "myfunc");
    ArgumentBuilder a1(_js,(char*)"str10", 0 , 0);
    ArgumentBuilder a2(_js,(char*)"num10", 0 , 0);
    ArgumentBuilder a3(_js,13, 0 , 0);
	int eret = _js.CompileExecuteFile("/home/libcdr/testcallback.js");
    if(JSVAL_IS_STRING(a1.getJSVal()))
    {
        printf("val str");
    }
    pdj->CallJSFunction("myfunc", fc, a1, a2, a3);
    return true;
}

bool anonymoustest::testExtStringSetLength(void)
{
	JSEngine _js;
	int iret =  _js.Init();
	int origsize , newsize;
	if(iret!=0)
	{
		printf("init failed\n");
	}
	defConsole(_js);
	jsvaltype _jsvaltype;
	AnonymousJSObject *pdj = new AnonymousJSObject("RECORD",_js);
	char * cstr ="anand";
	jsstringExt extstr(_js, &_jsvaltype, cstr , strlen(cstr )-2); 
	origsize = extstr.getJSStringSize();
	std::cout << " string before len = " << origsize << "\n";
	extstr.changeSize(extstr.getJSStringSize()+2);
	newsize = extstr.getJSStringSize();
	std::cout << " string after len = " << extstr.getJSStringSize() << std::endl ;
	
	//int eret = _js.CompileExecuteFile("/home/libcdr/test.js");
	return (newsize == origsize +2);
	
}

```

**Javascript Code using 'MyObject**'
```
   CONSOLE.writeln(RECORD.CALLER_NUMBER);
   CONSOLE.writeln(RECORD.CALLED_NUMBER);
   CONSOLE.writeln(RECORD.shx);
   RECORD.shx="5432 5432 5432 5432 5432 5432 5432 5432 5432 54321 53" ;
   RECORD.shx="5432 5432 5432 5432 5432 5432 5432 5432 48" ;
   RECORD.testfunc(); 
   RECORD.testfunca(" test string");
   RECORD.testfuncb(" test string b");
   RECORD.testfuncc(1234);
   RECORD.testfuncd(111.222); 
   RECORD.testfunc10args(1,2,3,4,5,6,7,8,9,10);
   RECORD.testfunc10argspvt(1,2,3,4,5,6,7,8,9,10);
   CONSOLE.writeln('hello \n') ;
```


### Example: Export Native Class ###
**C++ Code for exporting native C++ class as javascript object**

```
///C++ native class to be exported as javascript class


class HelpClass
{
public:
	HelpClass(){ printf("In default constructor of HelpClass \n"); }
	HelpClass(std::string&  p) { 
		printf("In constructor of HelpClass %s\n", p.c_str() );
		s=p;
	}
	std::string s;
public:
    
    //Call this function from javascript 
	int sfunc() { 
		std::cout << s << "\n"; 
		return s.size();
	}
    
    //Call this function from javascript 
    //And pass any javascript Object into this C++ Function 
    //and call an function associated with that object with arguments
	int sfuncCallback(AnonymousJSObjectWrapper& a) { 
        std::string sval = "1234 6";
        jsfunctionCall fc;
        fc.Init(a.getJSEngine(), a.getAnonymousJSObject().get()->getJSObject(), "myfunc");
        ArgumentBuilder a1(a.getJSEngine(), (char*)"str10", 0 , 0);
        ArgumentBuilder a2(a.getJSEngine(), (char*)"num10", 0 , 0);
        ArgumentBuilder a3(a.getJSEngine(), 13, 0 , 0);
        a.getAnonymousJSObject()->CallJSFunction("myfunc", fc, a1, a2, a3);
        return 1;
	}
    void helpme(char *p)
    {
        printf("helpme resolved %s\n",p);
    }
};

class Cc {
public:
	Cc(int pi) { 
		printf("In constructor of c %d\n",pi );
		i=pi;
		d=3.33;
		s="444";
	}

	char c;
	int i;
	double d;
	std::string s;
	HelpClass h;
public:
	int sfunc(HelpClass& a) { 
		std::cout <<  "cc::sfunc HelpClass&  \n";
        a.helpme("c++ Refrence issue");
		return 888;
	}
	int sfunc2(HelpClass* a) { 
		std::cout <<  "cc::sfunc2 HelpClass*  \n";
        a->helpme("c++ Pointer issue");
		return 999;
	}
	
};


bool exportclassTest::test(void)
{
	JSEngine _js;
	int iret =  _js.Init();
	if(iret!=0)
	{
		printf("init failed\n");
	}
	defConsole(_js);
	exporterCLass<HelpClass> *pech = exporterCLass<HelpClass>::CreateInstance("testHelpClass");
	pech = exporterCLass<HelpClass>::GetInstance();
	pech->setConstructor(init<std::string>()).
	add<int,&HelpClass::sfunc>("sfunc", &HelpClass::sfunc).
	add<int, AnonymousJSObjectWrapper&, &HelpClass::sfuncCallback >("sfuncCallback", &HelpClass::sfuncCallback).
	setVariable< std::string, &HelpClass::s >("svar");
	pech->registerEngine(_js);

	exporterCLass<Cc> *pec = exporterCLass<Cc>::CreateInstance("testclass");
	pec = exporterCLass<Cc>::GetInstance();
	pec->setConstructor(init<int >())
    .add<int, HelpClass&, &Cc::sfunc>("sfunc", &Cc::sfunc)
    .add<int, HelpClass*, &Cc::sfunc2>("sfunc2", &Cc::sfunc2)
	.setVariable< int , &Cc::i >("ivar")
	.setVariable< double, &Cc::d >("dvar")
	.setVariable< std::string, &Cc::s >("svar");
	pec->registerEngine(_js);
	AnonymousJSObject *pdj = new AnonymousJSObject("CallBackObj", _js);
	int eret = _js.CompileExecuteFile("/home/anandrathi/sre/libcdr/TestJSScriptExportClass.js");
}
		

```


**Javascript Code creating and using Export Native Class**

```
//#testclass.sfunc("Hello GC!\n")
//
//
//
CallBackObj.myfunc = function(arg1, arg2, arg3){ 

CONSOLE.writeln("myfunc entered"); 
CONSOLE.writeln(arg1.valueOf()); 
CONSOLE.writeln(arg2.valueOf()); 
CONSOLE.writeln(arg3.valueOf()); 
CONSOLE.writeln("myfunc exit"); 

 }

CONSOLE.writeln('hello \n') ;
var tHelpObj = new testHelpClass("HelloTest");
var rsf = tHelpObj.sfunc()
CONSOLE.write('rsf=') ;
CONSOLE.writeln(rsf) ;

var tObj = new testclass(2);
var rsf = tObj.sfunc(tHelpObj)

CONSOLE.write('rsf=') ;
CONSOLE.writeln(rsf) ;

rsf = tObj.sfunc2(tHelpObj)
CONSOLE.write('rsf2=') ;
CONSOLE.writeln(rsf) ;

CONSOLE.write('tObj.ivar=') ;
CONSOLE.writeln(tObj.ivar) ;
CONSOLE.write('tObj.dvar=') ;
CONSOLE.writeln(tObj.dvar) ;
CONSOLE.write('tObj.svar=') ;
CONSOLE.writeln(tObj.svar) ;

CONSOLE.write('callback=') ;
var rsfcb = tHelpObj.sfuncCallback(CallBackObj)
CONSOLE.writeln(rsf) ;


CONSOLE.writeln('done\n') ;

```