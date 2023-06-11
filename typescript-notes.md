# TypeScript Notes

## Declaring a function component in TS

In React, function components take a `props` argument and return a JSX element. When using TypeScript, you can (should?) declare the shape of the `props` object before you begin working.

You can declare the `props` using either `type` or `interface`. Using `interface` allows a consumer to extend it, if you are exporting.

    interface AppProps = {
        message: string;
        count: number;
    }

    type AppProps = {
        message: string;
    }

The easiest way to declare a Function Component; return type is inferred.

    const App = ({ message }: AppProps) => <div>{message}</div>;

You can annotate the return type so an error is raised if you returned some other type by accident.

    const App = ({ message }: AppProps): JSX.Element => <div>{message}</div>;