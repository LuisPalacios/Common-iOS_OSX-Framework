Introduction
============

How to create two frameworks, one for iOS and one for OSX, both sharing the same source and header files

Cada vez que quiero crear codigo compartible entre iOS y OSX termino con la pregunta ¿cómo se hacía para crear dos Frameworks, uno para iOS y otra para OSX que comparten el mismo código?. El objetivo de este documento es precisamente documentar "cómo hacerlo" y de paso devuelvo algo a la comunidad.

Este trabajo está basado en el de jverkoey: <a href="https://github.com/jverkoey/iOS-Framework">How to create, develop, and distribute iOS Static Frameworks quickly and efficiently</a>.

Para usar un ejemplo durante el documento he optado por algo real, voy a usar el proyecto **RNCryptor** <a href="https://github.com/RNCryptor/RNCryptor">CCCryptor (AES encryption) wrappers for iOS and Mac</a>, una librería que usa el mismo código tanto para iOS como para OSX y me viene de perlas para crear dos Frameworks (iOS y OSX) donde ambos referencian y usan los mismos fuentes y cabeceras .
<br />
<br />

<a rel="license" href="http://creativecommons.org/licenses/by/3.0/"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by/3.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/3.0/">Creative Commons Attribution 3.0 Unported License</a>.
<br /a>
<br /a>


Table of Contents 
===================

- [Frameworks para iOS y para OSX](#walkthrough)
  - [Resumen](#resumen)
- [License](#license)


<a name="walkthrough" />
Common iOS and OSX Frameworks
========================================

Objectie is to create two frameworks, one for iOS and a different one for OSX, however both will share same source and header files.

I'm using the RNCryptor project as an example, however I'll call my project with a different name in order to differentiate: "LPrncryptor".


<a name="overview" />
Overview
--------

First I'm going to create three directories, one for the common code and the other two for iOS and OSX Xcode frameworks. 


![image](http://)







Voy a crear tres directorios the project we are going to have three targets: a static library, a bundle, and an aggregate.

The static library target will build the source into a static library (.a) and specify which headers
will be "public", meaning they will be accessible from the .framework when we distribute it.

The bundle target will contain all of our resources and will be loadable from the framework.

The aggregate target will build the static library for i386/armv6/armv7/armv7s, generate the fat framework
binary, and also build the bundle. You will run this target when you plan to distribute the
.framework.



<a name="license" />
License
=======

Except as otherwise noted, the content of this page is licensed under the Creative Commons
Attribution 3.0 Unported License, and code samples are licensed under the Apache 2.0 License.

To view a copy of this license, visit http://creativecommons.org/licenses/by/3.0/ or send a letter
to Creative Commons, 444 Castro Street, Suite 900, Mountain View, California, 94041, USA.




