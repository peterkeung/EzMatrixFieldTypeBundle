EzMatrixFieldTypeBundle
=======================

Bundle provides ezmatrix field type for eZ Publish Platform 5.x and higher


#### Status: Incubation

Project is in incubation (stabilization) to be bundled with eZ Publish Platform
and supported by eZ Systems, when that happens project will be moved to ezsystems
organization and made part of future eZ Publish Platform releases.

### Install

From your [eZ Publish Platform](https://doc.ez.no/display/EZP/Installing+eZ+Publish+on+a+Linux-UNIX+based+system) install root with [composer installed](https://doc.ez.no/display/EZP/Using+Composer):

```
php -d memory_limit=-1 composer.phar require --prefer-dist ezsystems/ez-matrix-bundle:dev-master
```

### How to update content programmatically

Here's an example on how to update the value of a matrix field for a content item. The field has two columns, and we are creating two rows of content:

```
$repository = $this->getContainer()->get( 'ezpublish.api.repository' );
$contentService = $repository->getContentService();

// This example for setting a current user is valid for 5.x and early versions of 6.x installs
// This is deprecated from 6.6, and you should use PermissionResolver::setCurrentUserReference() instead
$repository->setCurrentUser( $repository->getUserService()->loadUser( 14 ) );

$contentId = 26926;
$newTitle = 'My updated title 2';

try
{
    // create a content draft from the current published version
    $contentInfo = $contentService->loadContentInfo( $contentId );
    $contentDraft = $contentService->createContentDraft( $contentInfo );

    // instantiate a content update struct and set the new fields
    $contentUpdateStruct = $contentService->newContentUpdateStruct();
    $contentUpdateStruct->initialLanguageCode = 'eng-US'; // set language for new version
    $matrixValue = new \EzSystems\MatrixBundle\FieldType\Matrix\Value(
        array(
            new \EzSystems\MatrixBundle\FieldType\Matrix\Row( array( 'col1' => 'row1col1', 'col2' => 'row1col2' ) ),
            new \EzSystems\MatrixBundle\FieldType\Matrix\Row( array( 'col1' => 'row2col2', 'col2' => 'row2col2' ) ),
        ),
        array(
            new \EzSystems\MatrixBundle\FieldType\Matrix\Column( array( 'name' => 'Column 1', 'id' => 'col1', 'num' => 0 ) ),
            new \EzSystems\MatrixBundle\FieldType\Matrix\Column( array( 'name' => 'Column 2', 'id' => 'col2', 'num' => 1 ) ),
        )
    );
    $contentUpdateStruct->setField( 'title', $newTitle );
    $contentUpdateStruct->setField( 'matrix', $matrixValue );
    // update and publish draft
    $contentDraft = $contentService->updateContent( $contentDraft->versionInfo, $contentUpdateStruct );
    $content = $contentService->publishVersion( $contentDraft->versionInfo );
}
catch ( \eZ\Publish\API\Repository\Exceptions\NotFoundException $e )
{
    $output->writeln( $e->getMessage() );
}
catch( \eZ\Publish\API\Repository\Exceptions\ContentFieldValidationException $e )
{
    $output->writeln( $e->getMessage() );
}
catch( \eZ\Publish\API\Repository\Exceptions\ContentValidationException $e )
{
    $output->writeln( $e->getMessage() );
}
```

### License & Copyright

See LICENSE file.


### Contributors 

https://github.com/ezcommunity/EzMatrixFieldTypeBundle/graphs/contributors
