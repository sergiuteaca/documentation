---
title: Debugging slow performance
description: Learn how to identify common problems with performance speeds and deploy solutions.
category:
  - debugging
category:
  - drupal

---


## Overview

When your site is fast, everybody wins. When it’s slow, nobody's happy... so how can you fix it? In this article, we’ll discuss the most common causes for performance problems, demonstrate how to diagnose bottlenecks, and provide actionable solutions for developers.

## Every PHP Error Slows Execution

An often ignored cause of bad performance is [PHP errors within site code](/docs/articles/errors/php-errors-and-exceptions/), as every single PHP error will slow your site down, including both notices and warnings that don’t crash your site.  








<colgroup>
		<col width="120">
		<col width="120">
		<col width="120">
		<col width="120">
		<col width="120">
		<col width="120">
		<col width="120">
	</colgroup><thead>
		<tr>
			<th> </th>
			<th>1 time</th>
			<th>25 times</th>
			<th>50 times</th>
			<th>100 times</th>
			<th>1,000 times</th>
			<th>10,000 times</th>
		</tr>
	</thead><tbody>
		<tr>
			<td><strong>none</strong></td>
			<td>0.00s</td>
			<td>0.08s</td>
			<td>0.17s</td>
			<td>0.30s</td>
			<td>3.04s</td>
			<td>32.81s</td>
		</tr>
		<tr>
			<td><strong>E_NOTICE</strong></td>
			<td>0.01s</td>
			<td>0.16s</td>
			<td>0.34s</td>
			<td>0.71s</td>
			<td>7.10s</td>
			<td>79.52s</td>
		</tr>
		<tr>
			<td><strong>E_WARNING</strong></td>
			<td>0.01s</td>
			<td>0.16s</td>
			<td>0.33s</td>
			<td>0.70s</td>
			<td>7.67s</td>
			<td>134.68s</td>
		</tr>
	</tbody>


Turning off error reporting suppresses the symptom, not the problem, and PHP execution will still be slow if there are errors.  














 ![](https://pantheon-systems.desk.com/customer/portal/attachments/200891)  




## Too Many Database Queries


The next performance killer is an excessive number of database queries per request. You can see that in your [New Relic dashboard](/docs/articles/sites/newrelic/new-relic-performance-analysis) by going to the Map tab, which will show you how the various low-level components in your application are performing together.  



 ![](https://pantheon-systems.desk.com/customer/portal/attachments/200890)Looking at an example, the average number of queries per request is shown in the lower-left, which in this case is 110 queries - a bit high in my opinion. In the upper-right, the average query duration is shown. That’s actually very respectable.  







## Not Caching

Non-optimized caching also is a huge problem. If you’re not caching anonymous pages, well, anonymous performance will be bad - especially as our Varnish caching respects your headers - which won’t be set if anonymous page caching is turned off.  










## Using the Database to Cache

By default, Drupal uses the database as a caching backend. This is an example of a fairly high traffic site, and as you can see, database cache hits are the vast majority of the slow queries.  


 ![](https://pantheon-systems.desk.com/customer/portal/attachments/200898)  







## Not Enough Traffic

The next problem is when a site doesn’t have enough traffic, which may seem paradoxical.  







- [Varnish](/docs/articles/architecture/edge/varnish) - spread out across multiple servers, and the cache is not shared between servers.
- [APC](/docs/articles/sites/what-is-apc-and-what-is-it-used-for/) - PHP has it’s own opcode cache, which is not shared between application servers.
- [Drupal](https://drupal.org/node/326504) and [redis](/docs/articles/sites/redis-as-a-caching-backend/) - Shared between your servers, but caches do have expirations, and if it’s old and stale, it’ll need to be regenerated.

## Too much traffic

Of course, too much site traffic can be a problem if you just don't have enough resources.  







<style type="text/css">.raw_data th {
  font-weight: bold;
  text-align: left;
}
.raw_data td {
  border: 1px solid black;
  font-family:courier new,courier,monospace;
}
</style>