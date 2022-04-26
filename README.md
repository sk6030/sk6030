- 👋 Hi, I’m @sk6030
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
-------------------------------------------------------------------------------------
-- Script de TNR (EXCEPT)
-------------------------------------------------------------------------------------

DECLARE @Suffixe_Source VARCHAR(255);
DECLARE @Suffixe_Cible VARCHAR(255);

SET @Suffixe_Source = '';
SET @Suffixe_Cible = '';

-------------------------------------------------------------------
-- On génère les SELECT
-------------------------------------------------------------------

DROP TABLE IF EXISTS #Tmp_ColonneExclues

CREATE TABLE #Tmp_ColonneExclues (
       Colonne VARCHAR(255)
)

----------------------------------------------------------------
-- Il faut lister les champs qui NE DOIVENT PAS être vérifiés
----------------------------------------------------------------
INSERT INTO #Tmp_ColonneExclues (Colonne)
SELECT 'dt_int_tech' UNION 
SELECT 'ExecutionId_dwh' UNION 
SELECT 'ActionId_Dwh';
----------------------------------------------------------------

WITH Liste_Table_Colonne AS
(
       Select distinct C2.TABLE_CATALOG + '.' + C2.TABLE_SCHEMA + '.' + C2.TABLE_NAME AS NomTable,
       substring((Select ', ['+C1.COLUMN_NAME + ']' AS [text()]
       From 
             ODV.INFORMATION_SCHEMA.COLUMNS  C1
       Where 
                    C1.TABLE_NAME = C2.TABLE_NAME
             and C1.COLUMN_NAME not in (select Colonne from #Tmp_ColonneExclues)
			 
       ORDER BY C1.TABLE_NAME
       For XML PATH ('')),2, 200000) AS Colonnes
       From 
             ODV.INFORMATION_SCHEMA.COLUMNS C2
             INNER JOIN ODV.INFORMATION_SCHEMA.TABLES T
                    ON           C2.TABLE_NAME = T.TABLE_NAME 
                          AND T.TABLE_TYPE = 'BASE TABLE'
						  and C2.TABLE_SCHEMA='isi'
						 
		 
)

/*****************************************************************
** Il faut décommenter le bloc ci-dessous à lancer (puis lancer la requête).
** Attention : 1 seul bloc doit être décommenté.
******************************************************************/
/*
 --Génération des "COUNT Source VS COUNT Cible"
SELECT 'SELECT ''' + NomTable + @Suffixe_Source + ''' AS NomTable_Source, Source.NbLigne_Source, ''' + NomTable + @Suffixe_Cible + ''' AS NomTable_Cible, Cible.NbLigne_Cible FROM (SELECT COUNT(1) NbLigne_Source FROM ' + NomTable + @Suffixe_Source + ') Source LEFT OUTER JOIN (SELECT COUNT(1) NbLigne_Cible FROM ' + NomTable + @Suffixe_Cible + ') Cible ON 1=1 UNION '
FROM Liste_Table_Colonne

*/

 --Génération des COUNT des "Source EXCEPT Cible"
SELECT 'SELECT ''' + NomTable + ''' AS NomTable, COUNT(1) AS NbDifferences FROM ( SELECT '+ Colonnes + ' FROM ' + NomTable + @Suffixe_Source + ' EXCEPT SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Cible + ') A UNION '
FROM Liste_Table_Colonne
/*
-- Génération des COUNT des "Cible EXCEPT Source"
SELECT 'SELECT ''' + NomTable + ''' AS NomTable, COUNT(1) AS NbDifferences FROM ( SELECT '+ Colonnes + ' FROM ' + NomTable + @Suffixe_Cible + ' EXCEPT SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Source + ') A UNION '
FROM Liste_Table_Colonne

-- Génération des "Source EXCEPT Cible"
SELECT 'SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Source + ' EXCEPT SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Cible + ';'
FROM Liste_Table_Colonne

-- Génération des "Cible EXCEPT Source"
SELECT 'SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Cible + ' EXCEPT SELECT ' + Colonnes + ' FROM ' + NomTable + @Suffixe_Source + ';'
FROM Liste_Table_Colonne

*/





--->


