##############################
设计与架构目标
##############################

CodeIgniter 的目标是在最小化，最轻量级的开发包中得到最大的执行效率、功能和灵活性。

为了达到这个目标，我们在开发过程的每一步都致力于基准测试、重构和简化工作，
拒绝加入任何对实现目标没有帮助的东西。

从技术和架构角度看，CodeIgniter 按照下列目标创建：

-  **Dynamic Instantiation.** In CodeIgniter, components are loaded and
   routines executed only when requested, rather than globally. No
   assumptions are made by the system regarding what may be needed
   beyond the minimal core resources, so the system is very light-weight
   by default. The events, as triggered by the HTTP request, and the
   controllers and views you design will determine what is invoked.
-  **Loose Coupling.** Coupling is the degree to which components of a
   system rely on each other. The less components depend on each other
   the more reusable and flexible the system becomes. Our goal was a
   very loosely coupled system.
-  **Component Singularity.** Singularity is the degree to which
   components have a narrowly focused purpose. In CodeIgniter, each
   class and its functions are highly autonomous in order to allow
   maximum usefulness.

CodeIgniter is a dynamically instantiated, loosely coupled system with
high component singularity. It strives for simplicity, flexibility, and
high performance in a small footprint package.
