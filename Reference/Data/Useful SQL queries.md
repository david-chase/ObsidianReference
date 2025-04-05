#sql #query

Select only records starting with the letters A-D
	`SELECT * FROM Table WHERE field LIKE '[a-d]%'

Sorting results
	`SELECT * from Table WHERE Field LIKE % ORDER BY Name DEC
	`SELECT * from Table WHERE Field LIKE % ORDER BY Name ASC
	