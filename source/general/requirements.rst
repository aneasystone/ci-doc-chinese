###################
服务器要求
###################

推荐使用 `PHP <http://php.net/>`_ 5.4 或更新版本。

虽然 CodeIgniter 也可以在 PHP 5.2.4 上运行，但是出于潜在的安全和性能问题，
我们强烈建议你不要使用这么老版本的 PHP，而且老版本的 PHP 也会缺少很多特性。

大多数的 Web 应用程序应该都需要一个数据库。当前 CodeIgniter 支持下列数据库：

  - MySQL (5.1+)，驱动有：*mysql* （已废弃），*mysqli* 和 *pdo*
  - Oracle，驱动有：*oci8* 和 *pdo*
  - PostgreSQL，驱动有：*postgre* 和 *pdo*
  - MS SQL，驱动有：*mssql*，*sqlsrv* （2005及以上版本）和 *pdo*
  - SQLite，驱动有：*sqlite* （版本2），*sqlite3* （版本3）和 *pdo*
  - CUBRID，驱动有：*cubrid* 和 *pdo*
  - Interbase/Firebird，驱动有：*ibase* 和 *pdo*
  - ODBC：驱动有：*odbc* 和 *pdo* （需要知道的是，ODBC 其实只是数据库抽象层）