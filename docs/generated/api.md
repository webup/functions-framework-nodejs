## API Report File for "@openfunction/functions-framework"

> Do not edit this file. It is a report generated by [API Extractor](https://api-extractor.com/).

```ts

/// <reference types="node" />

import { CloudEventV1 as CloudEvent } from 'cloudevents';
import { Request as Request_3 } from 'express';
import { Response as Response_2 } from 'express';

export { CloudEvent }

// @public
export const cloudEvent: <T = unknown>(functionName: string, handler: CloudEventFunction<T>) => void;

// @public
export interface CloudEventFunction<T = unknown> {
    // (undocumented)
    (cloudEvent: CloudEvent<T>): any;
}

// @public
export interface CloudEventFunctionWithCallback<T = unknown> {
    // (undocumented)
    (cloudEvent: CloudEvent<T>, callback: Function): any;
}

// @public
export interface CloudFunctionsContext {
    eventId?: string;
    eventType?: string;
    resource?: string | {
        [key: string]: string;
    };
    timestamp?: string;
}

// @public
export enum ComponentType {
    Binding = "bindings",
    PubSub = "pubsub"
}

// @public
export type Context = CloudFunctionsContext | CloudEvent<unknown>;

// @public
export class ContextUtils {
    static IsAsyncRuntime(context: OpenFunctionContext): boolean;
    static IsBindingComponent(component: OpenFunctionComponent): boolean;
    static IsKnativeRuntime(context: OpenFunctionContext): boolean;
    static IsPubSubComponent(component: OpenFunctionComponent): boolean;
}

// @public
export interface Data {
    // (undocumented)
    data: object;
}

// @public
export interface EventFunction {
    // (undocumented)
    (data: {}, context: Context): any;
}

// @public
export interface EventFunctionWithCallback {
    // (undocumented)
    (data: {}, context: Context, callback: Function): any;
}

// @public
export type HandlerFunction<T = unknown> = HttpFunction | EventFunction | EventFunctionWithCallback | CloudEventFunction<T> | CloudEventFunctionWithCallback<T> | OpenFunction;

// @public
export const http: (functionName: string, handler: HttpFunction) => void;

// @public
export interface HttpFunction {
    // (undocumented)
    (req: Request_2, res: Response_2): any;
}

// @public
export type LegacyCloudFunctionsContext = CloudFunctionsContext | Data;

// @public
export interface LegacyEvent {
    // (undocumented)
    context: CloudFunctionsContext;
    // (undocumented)
    data: {
        [key: string]: any;
    };
}

// @public
export interface OpenFunction {
    // Warning: (ae-forgotten-export) The symbol "OpenFunctionRuntime" needs to be exported by the entry point index.d.ts
    //
    // (undocumented)
    (ctx: OpenFunctionRuntime, data: {}): any;
}

// @public
export interface OpenFunctionBinding {
    [key: string]: OpenFunctionComponent;
}

// @public
export interface OpenFunctionComponent {
    componentName: string;
    componentType: `${ComponentType}.${string}`;
    metadata?: {
        [key: string]: string;
    };
    operation?: string;
    uri?: string;
}

// @public
export interface OpenFunctionContext {
    inputs?: OpenFunctionBinding;
    name: string;
    outputs?: OpenFunctionBinding;
    port?: string;
    runtime: keyof typeof RuntimeType;
    version: string;
}

// @public (undocumented)
interface Request_2 extends Request_3 {
    rawBody?: Buffer;
}
export { Request_2 as Request }

export { Response_2 as Response }

// @public
export enum RuntimeType {
    Async = "Async",
    Knative = "Knative"
}

// (No @packageDocumentation comment for this package)

```
