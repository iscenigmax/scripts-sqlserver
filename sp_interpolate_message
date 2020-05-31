IF EXISTS (SELECT name FROM sysobjects WHERE name = 'sp_interpolate_message' AND type = 'P')
	DROP PROCEDURE sp_interpolate_message;
GO
CREATE PROCEDURE sp_interpolate_message	
(
	@MESSAGE NVARCHAR(MAX) OUTPUT,
	@MESSAGE_QUERY NVARCHAR(MAX)
)
WITH ENCRYPTION
AS
BEGIN
	--running the query to fill the global temp
	EXEC SP_EXECUTESQL @MESSAGE_QUERY

	--number of columns of the temporary
	DECLARE @COLUMNS INT SET @COLUMNS=(SELECT COUNT(*) FROM tempdb.sys.columns WHERE object_id=Object_id('tempdb..##T_INTERPOLATE'))
	--interaction counter
	DECLARE @COUNT INT SET @COUNT=1;

	--loop
	WHILE @COUNT <= @COLUMNS
	BEGIN
		DECLARE @COLUMN NVARCHAR(MAX)	SET @COLUMN=CONVERT(NVARCHAR,@COUNT)
		--replace the result value of the loop position in the text string of the menjsae [position] with {position}, one by one
		SET @MESSAGE_QUERY = N'SET @DATA_OUT=(SELECT REPLACE(@DATA_OUT,''{' + @COLUMN + '}'', [' + @COLUMN + ']) FROM ##T_INTERPOLATE)'

		--apply the replacement and the result keeps it in the message variable that is replacing the columns
		EXEC SP_EXECUTESQL 
			@MESSAGE_QUERY, 
			N'@data_out NVARCHAR(MAX) OUTPUT',
			@DATA_OUT=@MESSAGE OUTPUT;
		
		SET @COUNT = @COUNT + 1;
	END

	IF OBJECT_ID('tempdb..##T_INTERPOLATE') IS NOT NULL 
		DROP TABLE ##T_INTERPOLATE;

END
GO

--way of execution
DECLARE @MESSAGE VARCHAR(MAX), @MESSAGE_QUERY VARCHAR(MAX)
--message to interpolate with the columns of the query, each column must have {column number of the query}
SET @MESSAGE = 'Today {1} a query interpolation process was carried out with a text message on the server {2} with version {3}'
--each column must be named with a position number in the message {NUMBER} by [NUMBER] and add the result in a global temporary table INTO ## T_INTERPOLATE for use in the procedure
SET @MESSAGE_QUERY = N'
	SELECT
		CONVERT(VARCHAR(10),GETDATE(),121) [1],
		@@SERVERNAME [2],
		@@VERSION [3]
	INTO ##T_INTERPOLATE'

--the message and the query are sent, the interpolation result is returned on the message variable
EXEC sp_interpolate_message @MESSAGE OUTPUT, @MESSAGE_QUERY

--result
SELECT @MESSAGE
